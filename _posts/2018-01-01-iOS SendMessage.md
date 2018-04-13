---
layout:     post
title:      iOS消息发送与转发理解
date:       2018-01-01
author:     "caoyq"
header-img: "img/post-bg-1.jpg"
tags:
    - iOS
---

> 消息的发送与转发还是利用到oc中的黑魔法runtime实现的

### 消息发送

什么称之为消息发送？在oc中方法的调用称之为向对象发送消息。

```
//B_ViewController.h
@interface B_ViewController : UIViewController

- (void)sendMessageTest;

@end

//B_ViewController.m
@implementation B_ViewController

- (void)sendMessageTest
{
    NSLog(@"sendMessageTest");
}

@end

在A_ViewController中初始化并调用方法
B_ViewController *bClass = [B_ViewController new];
[bClass sendMessageTest];
```

上面是在A类中调用B类的实例方法简单模式

当我们将 `B_ViewController.m`中的实现方法屏蔽掉之后，会发现系统运行后报错了![](/img/in-post/iOSSendMessage/文件.m中不实现方法出现的错误.png)

这就是要研究的问题:

* [bClass sendMessageTest]  是如何调用的
* 没有实现方法报错的原因



#### [bClass sendMessageTest] 是如何实现的

```
[bClass sendMessageTest] 
	   ||
       || 编译后，实际是调用下面的objc_msgSend方法
       ||
((void (*)(id, SEL))(void *) objc_msgSend)(bClass, @selector(sendMessageTest));
```

本质上是 runtime 在运行时期间，根据传递的两个参数来寻找到对应的实例方法

#### runtime如何找到对应的实例方法

##### isa指针如何关联

先要弄明白 实例对象、类对象、meta类对象、根meta类对象 它们之间的联系，下图简单标明了

![](/img/in-post/iOSSendMessage/guanxitu.png)



从上面是可以看出：它们之间是通过一个叫 isa 的指针来关联起来.

那么具体的关系可以概述为：

> 实例对象通过 isa 指针，找到类对象 Class；类对象同样通过 isa 指针，找到元类对象；元类对象也是通过 isa 指针，找到根元类对象；最后，根元类对象的 isa 指针，指向自己。可以发现 NSObject 是整个消息机制的核心，绝大数对象都继承自它。



##### isa指针的寻找过程

1、先看下系统内部的`object 结构体`

```
struct objc_object {

Class _Nonnull isa  OBJC_ISA_AVAILABILITY;

};
```

可以看出 只有一个指向Class的指针

2、接下里再来看下 `Class 结构体`

```
struct objc_class {

Class _Nonnull isa  OBJC_ISA_AVAILABILITY;

#if !__OBJC2__

Class _Nullable super_class  OBJC2_UNAVAILABLE;

const char * _Nonnull name OBJC2_UNAVAILABLE;

long version OBJC2_UNAVAILABLE;

long info  OBJC2_UNAVAILABLE;

long instance_size OBJC2_UNAVAILABLE;

struct objc_ivar_list * _Nullable ivars  OBJC2_UNAVAILABLE;

struct objc_method_list * _Nullable * _Nullable methodLists  OBJC2_UNAVAILABLE;

struct objc_cache * _Nonnull cache OBJC2_UNAVAILABLE;

struct objc_protocol_list * _Nullable protocols  OBJC2_UNAVAILABLE;

#endif

} OBJC2_UNAVAILABLE;
```

从上面可以看出 有 `objc_method_list`这个结构体的存在，

3、我们来看下 `objc_method_list`结构体

```
struct objc_method_list {

struct objc_method_list * _Nullable obsolete OBJC2_UNAVAILABLE;

int method_count OBJC2_UNAVAILABLE;

#ifdef __LP64__

int space  OBJC2_UNAVAILABLE;

#endif

struct objc_method method_list[1]  OBJC2_UNAVAILABLE;

}
```

从上面可以看出 又存在另一个 `objc_method`的结构体

4、来看下`objc_method`结构体

```
struct objc_method {

SEL _Nonnull method_name OBJC2_UNAVAILABLE;

char * _Nullable method_types  OBJC2_UNAVAILABLE;

IMP _Nonnull method_imp  OBJC2_UNAVAILABLE;

}
```

method里面保存了三个参数

* 方法名称
* 方法类型
* 方法的IMP（IMP与SEL是键值对）

**经过一层层的深入，大致就明白了 实例对象调用方法的逻辑**

- 先被编译成  ((void (*)(id, SEL))(void *) objc_msgSend)(bClass, @selector(sendMessageTest));
- 沿着入参 bClass 的 isa 指针，找到 bClass 的类对象（Class)
- 接着在 bClass 的方法列表 methodLists 中，找到对应的 Method
- 最后找到 Method 中的 IMP 指针，从而得到具体实现方法SEL



#### runtime如何找到对应的类方法

其实类方法也一样，传入的不是实例对象，而是一个类

```
[B_ViewController sendMessageClassMethod];
```

那么我们首先通过类的isa指针，找到其meta类，在meta类中也存在`methodList`，步骤就和寻找实例方法一样



#### runtime寻找的效率

上面寻找实例方法、类方法都是是作了一个最简单的说明，都是在子类中就能寻找到，如果在子类中寻找不到，那么就会一级一级往父类寻找，同一个方法寻找对此又是如何实现的呢？

```
for (int i = 0; i < 100000; ++i) 
{
    BClass *bObject = [BClass new];
    [bObject method];
}
```

上面的无限大循环就是一个很明显的例子，不可能说每次寻找都会一层一层去遍历，所以就引入了 `Class Cache` 

##### 类缓存

>Class Cache 认为，当一个方法被调用，那么它之后被调用的可能性就越大。

回头看下上面的 `Class 结构体`，会发现中间就有一项

```
struct objc_cache * _Nonnull cache OBJC2_UNAVAILABLE;
```

这个缓存是只存在于类中，对象中是不存在的

##### 如何利用缓存进行遍历

查找方法时，会先从缓存中查找，找到直接返回 ；找不到，再去 Class 的方法列表中找。

子类没有就去父类寻找，找到之后也会同时将其添加到缓存

![遍历寻找方法以及添加到缓存的流程](/img/in-post/iOSSendMessage/seekSelector.png)

上面所说的都是成功寻找到了，还有可能找不到，这个怎么处理呢？

就是下面的消息转发



### 消息转发

#### 没有实现方法报错的原因

这是针对上面的第二个问题作出回答，寻找不到方法并不会直接报错，系统有三次补救的机会，三次补救机会都没有找到方法实现才会crash

未实现的方法

```
// ViewController.m 中 （未实现 myLogAction 方法）
[self performSelector:@selector(myLogAction:) withObject:nil];
```



#### 第一次补救

```
//针对实例方法
+ (BOOL)resolveInstanceMethod:(SEL)sel;
//针对类方法
+ (BOOL)resolveClassMethod:(SEL)sel;
```

使用示例：

```
// ViewController.m 中

void myMethod(id self, SEL _cmd,NSString *nub) {
	NSLog(@"%@",nub);
}

+ (BOOL)resolveInstanceMethod:(SEL)sel 
{
#pragma clang diagnostic push

#pragma clang diagnostic ignored "-Wundeclared-selector"

    if (sel == @selector(myLogAction:)) {

#pragma clang diagnostic pop

        class_addMethod([self class],sel,(IMP)myMethod,"newMethodDesc");
        return YES;
    }else {
        return [super resolveInstanceMethod:sel];
    }
}
```

我们只需要在 `resolveInstanceMethod:` 方法中，利用 `class_addMethod` 方法，将未实现的 `myLogAction`: 绑定到 `myMethod` 上就能完成转发，最后返回 YES。



#### 第二次补救

```
- (id)forwardingTargetForSelector:(SEL)aSelector;
```

这个方法要求返回一个 id。使用场景一般是将 A 类的某个方法，转发到 B 类的实现中去。

使用示例：

这里就是 VC中的方法，转发到 BVC类的实现中去

```

// BVC.m
@implementation BVC

- (void)myLogAction:(NSString *)str {
	NSLog(@"%@",str);
}

@end


// ViewController.m 中

- (id)forwardingTargetForSelector:(SEL)aSelector {

#pragma clang diagnostic push

#pragma clang diagnostic ignored "-Wundeclared-selector"

    if (aSelector == @selector(myLogAction:)) {

#pragma clang diagnostic pop

        return [BVC new];
    }else{
        return [super forwardingTargetForSelector:aSelector];
    }
}
```



#### 第三次补救

```
- (NSMethodSignature *)methodSignatureForSelector:(SEL)aSelector;

- (void)forwardInvocation:(NSInvocation *)anInvocation;
```

第一个要求返回一个方法签名，第二个方法转发具体的实现。二者相互依赖，只有返回了正确的方法签名，才会执行第二个方法。

这次的转发作用和第二次的比较类似，都是将 A 类的某个方法，转发到 B 类的实现中去。不同的是，第三次的转发相对于第二次更加灵活，**forwardingTargetForSelector: 只能固定的转发到一个对象；forwardInvocation:  可以让我们转发到多个对象中去**。

使用示例：

```
// BVC.m
@implementation BVC

- (void)myLogAction:(NSString *)str {
	NSLog(@"%@",str);
}

@end


// CVC.m
@implementation CVC

- (void)myLogAction:(NSString *)str {
	NSLog(@"%@",str);
}

@end



// ViewController.m 中

- (NSMethodSignature *)methodSignatureForSelector:(SEL)aSelector {

#pragma clang diagnostic push

#pragma clang diagnostic ignored "-Wundeclared-selector"

	if (aSelector == @selector(myLogAction:)) {

	#pragma clang diagnostic pop

	return [NSMethodSignature  signatureWithObjCTypes:"methodSignatureDesc"];

	}

	return [super methodSignatureForSelector:aSelector];

}

- (void)forwardInvocation:(NSInvocation *)anInvocation {

	BVC *b = [BVC new];
	CVC *c = [CVC new];

	if ([b respondsToSelector:anInvocation.selector]) {

		[anInvocation invokeWithTarget:b];

	}

	if ([c respondsToSelector:anInvocation.selector]) {

		[anInvocation invokeWithTarget:c];

	}
}
```



如果到了第三次机会，还没找到对应的实现，就会 crash：

> unrecognized selector sent to instance 0x7f9f817072b0