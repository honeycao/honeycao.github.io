---
layout:     post
title:      组件化之私有库的创建
subtitle:   
date:       2018-05-23
author:     "AllenCao"
header-img: "img/post-bg-2.png"
tags:
    - 私有库
    - 组件化
---

[TOC]

### 本地私有库

一套流程走下去，能完整实现自己的私有库，这就足够了。

#### 根据模板创建本地私有库

通过pod命令语句，创建一个库的模板

```
$ pod lib create ACTools
```

接着会从 Cocoapods/pod-template.git  导入模板到ACTools

让我们填写几个问题，问题的答案基本都是固定的

![image-1](/img/in-post/RemotePrivateLibrary/image-1.png)

第三条选择 Yes，那么会生成一个demo工程

回车创建成功，会自动打开生成的demo工程，我们的私有库就在Pods下面

![image-1](/img/in-post/RemotePrivateLibrary/image-2.png)



模板创建具有3个优点

* 已经创建好ACTools.podspec
* git管理
* demo工程

手动创建也是可以的，不过相对于模板创建的话就显得复杂点，因为上面3个条件都要一步一步去实现，就不介绍了，可以看看末尾关于手动创建私有库的的参考文章

#### 完善私有库，添加文件

现在我们可以向私有库中添加我们的代码，一定要从物理路径下(这里就是Classes文件下)添加，并删除原有的 ReplaceMe.m 文件

![image-1](/img/in-post/RemotePrivateLibrary/image-3.png)

#### 使用本地私有库

上面一步只是在私有库中添加了代码，Example 这个demo工程里面并没有，所以我们需要更新下

```
//cd到Example路径下
$ pod install
```

然后就能看到如下结果

![image-1](/img/in-post/RemotePrivateLibrary/image-4.png)



此时的 ACTools 已经是一个本地的私有库了，如何在其他地方去调用？

其实在生成的demo工程中，Podfile里面已经演示了如何使用

```
pod 'ACTools', :path => '../'
```

在本地其他项目中，就不是这么写了，因为path不同，在本地其他地方使用的话，只要找到ACTools的绝对路径就行



### 远程私有库的创建

其实远程是基于本地私有库的一个拓展

.spec文件：是一个索引库，根据这个索引库才能找到我们的库，目前在本地所以就是一个本地库，github上的CocoaPods就有一个单独存放.spec文件的Spec文件夹，我们开源的第三方都是将自己的.spec文件上传到这个上面，然后其他人才能够利用CocoaPods导下来

知道了原理，那么我们本地的私有库肯定也是需要一个存放私有Spec文件的文件夹

#### 创建远程私有索引库

目前可以创建私有库的地方一般就是：码云gitee、gitlab

以gitee为例

在自己的码云上创建一个私有的索引库，创建过程就写了，直接得到我们创建的私有索引库的地址

```
https://gitee.com/honeycao/ACSpecs
```



#### 将创建的索引库添加到本地CocoaPods仓库

cocoapods本地仓库路径

```
$ cd ~/.cocoapods/repos
```

进入上面路径并添加自己的远程索引库

```
$ pod repo add ACSpecs https://gitee.com/honeycao/ACSpecs
```

![image-1](/img/in-post/RemotePrivateLibrary/image-5.png)

默认情况下是只有一个master仓库，我们常用的SDWebImage,AFNetWoking等的本地索引就在这个仓库中。现在是通过码云来创建自己的仓库，所以要创建一个自己的索引仓库，该仓库下也只是存放各组件的索引文件(.spec文件)，不存放代码！而且只需要第一次使用自己的私有库时才需要创建，后面就不需要了。



#### 创建私有库的远程仓库

同样的道理，目前我们的私有库是在本地，那么也需要相应的创建一个远程私有库来存放它。

按照创建私有索引库的方法，在码云上再创建一个远程私有库，由于本地已经有了私有库，所以我们在创建的时候就不用选择生成一个Readme文件，直接点击创建，会生成一个空的远程项目

![image-1](/img/in-post/RemotePrivateLibrary/image-7.png)

第一步：执行下面命令，上传本地私有库的模板

```
$ cd  cd ~/Desktop/ACTools
$ git remote add origin https://gitee.com/honeycao/ACTools.git
$ git push -u origin master
```

第二步：提交本地代码，推送到远端

```
$ git add .
$ git commit  -m  '初始化'
$ git push -u origin master
```



#### 更改本地私有库的索引文件.Spec

代码已经上传到远程了，那么索引库中要进行相应的修改，才能找得到我们的库

在模板中修改其中两项项就行，之前模板是按照GitHub上来填写的，现在改为刚才上一步创建的私有远程仓库地址

```
s.homepage         = 'https://gitee.com/honeycao/ACTools'
s.source           = { :git => 'https://gitee.com/honeycao/ACTools.git', :tag => s.version.to_s }
```



#### 给版本打一个tag

```
// 0.1.0是与.spec一致
$ git tag 0.1.0
$ git push  --tags
```



#### 验证.Spec文件

之所以把验证放在tag之后，就是因为如何先验证那么一定会报错，错误就是找不到与spec对应的tag

```
$ pod spec lint
//出现由于警告没过的，可以加上 --allow-warnings
$ pod spec lint --allow-warnings
//也有加上 --private
$ pod spec lint --private
```



#### 建立关联

本地的spec文件是已经合格了，那么现在需要放到远程的spec索引库中

```
$ pod repo push ACSpecs ACTools.podspec
//上面验证的时候用到 --allow-warnings，这里也需要加上
$ pod repo push ACSpecs ACTools.podspec --allow-warnings
```

其实远程的spec索引库在本地已经存在了，前面已经添加到本地的Cocoapods仓库了，所以我们添加成功后，就已经可以看到了

![image-1](/img/in-post/RemotePrivateLibrary/image-8.png)

通过这一步我们就同步好了远程和本地索引库，到此，私有库已经制作完毕！



### 使用自己的私有库

直接pod导入是会出错的，至于原因我们可以查看下

```
//查看本地的索引库
$ pod repo
```

![image-1](/img/in-post/RemotePrivateLibrary/image-10.png)

会发现github上的开源第三方索引库都是来自`CocoaPods/Specs.git`

我们的私有库索引库是放在自己的远程索引库中

Podfile 中正确写法如下，两者都不缺少，上面是导入自己的私有库来源，下面是导入github第三方的开源库来源

```
source 'https://gitee.com/honeycao/ACSpecs'
source 'https://github.com/CocoaPods/Specs.git'
```

`pod install`之后就可以看到自己的私有库了，目前就到这里，库的更新就和开源第三方的更新一样，有时候后面简单说下。

其实也就两步：远程私有代码库更新tag，推送新版本的spec到远程索引库即可。



### 后续问题

#### 关联私有的Spec文件出错

```
$ pod repo push MySpecs test.podspec --allow-warnings
```

执行上面的关联方法，可能会碰到下面的错误

 `The repo MySpecs at ../../../.cocoapods/repos/MySpecs is not clean`

解释：这个是由于本地的Specs库 被修改，本地没有提交一些东西，我们可以进入Specs目录，进行清空

```
$ cd ~/.cocoapods/repos/MySpecs   
$ git clean -f
```

上面步骤依旧解决不了问题，那么我们就需要看看具体的版本管理了

```
//查看具体版本控制的记录
$ git status    
//该push的push 保持git的干净
```

目前自己遇到的问题，经上个步骤已解决了，自己是由于 私有库.podspec文件修改频繁，没有每次都push到远程的specs库，所以本地存在没有提交的文件。



### 参考资料

1、[如果需要知道导入私有索引库到本地Cocoapods的另外一种方式(需要公钥和私钥)，可以了解下](https://www.jianshu.com/p/5fe1a67f3ee1)

2、[如何不使用模板，手动去创建一个本地的私有库](https://www.jianshu.com/p/5b6e1b2f6bb8)