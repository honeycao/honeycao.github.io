---
layout:     post
title:      framework及其制作
subtitle:   学习SDK中的framework的制作方法
date:       2017-07-15
author:     "caoyq"
header-img: "img/post-bg-1.jpg"
catalog: true
tags:
    - SDK
---


### framework了解

**一、什么是库？**

> 库是共享程序代码的方式，一般分为静态库和动态库。

**二、静态库和动态库的区别**

> 静态库：链接时完整地拷贝至可执行文件中，被多次使用就有多份冗余拷贝。
>
> 动态库：链接时不复制，程序运行时由系统动态加载到内存，供程序调用，系统只加载一次，多个程序共用，节省内存。

**三、iOS里静态库形式**

> `.a` 和 `.framework`

**四、iOS里动态库形式**

> `.dylib` 和 `.framework`

**五、framework为什么既是静态库又是动态库？**

> 系统的`.framework`是动态库，我们自己创建的`.framework`是静态库。

**六、.a 和 .framework 有什么区别**

> `.a` 是一个纯二进制文件，`.framework`中除了有二进制文件之外还有资源文件。
>
> `.a` 文件不能直接使用，至少要有 `.h` 文件配合，`.framework` 文件可以直接使用。
>
> `.a + .h + sourceFile = .framework`

**七、为什么要使用静态库？**

> 方便共享代码，便于合理使用。
>
> 实现iOS程序的模块化，可以把固定业务模块化成静态库。
>
> 和别人分享你的代码库，但是不想让别人看到你的代码实现。
>
> 开发第三方sdk的需求。

------

### 制作 framework
>Xcode版本： 8.3.2 (8E2002)

#### 1、Xcode 中创建一个空的 framework

> Xcode:  `File -> New -> Project -> Framework & Library -> Cocoa Touch Framework`

![create_empty_framework](/img/in-post/framework/create_empty_framework.png)


#### 2、更改参数

**2.1、`TARGET -> Build Settings -> Architectures`**

![fk_architecture](/img/in-post/framework/fk_architecture.png)

**2.2、`TARGET -> Build Settings -> Linking` 下更改几个参数**

![](/img/in-post/framework/fk_Linking.png)

**2.3、对于使用`category`时，必须配置 `Other Linker Flags` 为 `-ObjC`**

#### 3、创建自己功能类代码

![fk_demo_style](/img/in-post/framework/fk_demo_style.png)

#### 4、设置 Headers 和 Resources

> 在`TARGETS` -> `Build Phases` -> `Headers` 中添加头文件，Public 中添加公开的头文件 
>
> 在`TARGETS` -> `Build Phases` -> `Copu Bundle Resources` 中添加framework中使用的bundle资源文件

![fk_headers_resources](/img/in-post/framework/fk_headers_resources.png)

------

### 编译与合并

> 合并则分为两种，一个手动，一个脚本
>
> 如果打包出去只需要真机或者模拟器中的一种，那么我们只需编译就可以得到，合并是为了同时满足模拟器和真机进行的操作。

**1、在模拟器 和 真机 环境下分别编译我们的 framework**

> 随便选择一个`simulator`和 `Generic Device`(代表真机) 分别编译 command + B

**2、选中我们的 framework，右键在Finder中显示，并找到对应环境 .framework文件夹下的文件**

> `Debug-iphoneos` 代表debug状态下真机编译结果 
>
> `Debug-iphonesimulator` 代表debug状态下模拟器编译结果 
>
> 在真实上架项目中，我们就是需要编译`release`版本 

![fk_ShowInFinder](/img/in-post/framework/fk_ShowInFinder.png)

![fk_finder_debug](/img/in-post/framework/fk_finder_debug.png)



**3、将模拟器和真机编译得到的文件进行合并，生成一个真机模拟器同时兼容的文件**

**3.1、手动合并**

* 终端输入命令语句

      合并的文件路径：在某个地方创建一个文件，命名和我们编译得到文件的名字一致，然后拖拉文件到终端即可得到路径
      在终端中使用命令时，这些路径都只要直接托拉过来
      
      $ lipo -create 真机文件路径 模拟器文件路径 -output 合并得到的文件路径


* 合并得到文件 替换 真机编译得到的`.framework`文件下的文件（或者用模拟器编译得到的也一样），即可得到最终模拟器和真机都可使用的 `framework`

  ![fk_hebing](/img/in-post/framework/fk_hebing.png)

**3.2、脚本合并**

* Xcode: `File -> New -> Target`  在当前库路径下创建脚本所需的文件，任意命名即可

  ![fk_new_Aggregate](/img/in-post/framework/fk_new_Aggregate.png)

* 添加运行的脚本

  ![fk_new_runscript](/img/in-post/framework/fk_new_runscript.png)

  ![fk_script_contents](/img/in-post/framework/fk_script_contents.png)

* 具体脚本内容

      # Sets the target folders and the final framework product.
      # 如果工程名称和Framework的Target名称不一样的话，要自定义FMKNAME
      # 例如: FMK_NAME = "MyFramework"
      FMK_NAME=${PROJECT_NAME}
      # Install dir will be the final output to the framework.
      # The following line create it in the root folder of the current project.
      INSTALL_DIR=${SRCROOT}/Products/${FMK_NAME}.framework
      # Working dir will be deleted after the framework creation.
      WRK_DIR=build
      DEVICE_DIR=${WRK_DIR}/Release-iphoneos/${FMK_NAME}.framework
      SIMULATOR_DIR=${WRK_DIR}/Release-iphonesimulator/${FMK_NAME}.framework
      # -configuration ${CONFIGURATION}
      # Clean and Building both architectures.
      xcodebuild -configuration "Release" -target "${FMK_NAME}" -sdk iphoneos clean build
      xcodebuild -configuration "Release" -target "${FMK_NAME}" -sdk iphonesimulator clean build
      # Cleaning the oldest.
      if [ -d "${INSTALL_DIR}" ]
      then
      rm -rf "${INSTALL_DIR}"
      fi
      mkdir -p "${INSTALL_DIR}"
      cp -R "${DEVICE_DIR}/" "${INSTALL_DIR}/"
      cp -R "${SIMULATOR_DIR}/" "${INSTALL_DIR}/"
      # Uses the Lipo Tool to merge both binary files (i386 + armv6/armv7) into one Universal final product.
      lipo -create "${DEVICE_DIR}/${FMK_NAME}" "${SIMULATOR_DIR}/${FMK_NAME}" -output "${INSTALL_DIR}/${FMK_NAME}"
      rm -r "${WRK_DIR}"
      open "${INSTALL_DIR}"

* 编译脚本，同前面编译framework一样，编译完成会自动弹出真机和模拟器都可使用的`framework`

  ![fk_run_script](/img/in-post/framework/fk_run_script.png)

------

### 在其他地方使用我们的 framework

**1、将符合要求的 framework 拖拉到其他项目中，如果有 bundle 资源文件，那么也必须单独拖拉一份到项目中去**

![fk_use](/img/in-post/framework/fk_use.png)

**2、检查是否添加完全**

![fk_check](/img/in-post/framework/fk_check.png)

------

### 注意事项
**1、当`framework`不是同时支持模拟器和真机的时候，运行项目则会出现以下的问题**
![fk_check](/img/in-post/framework/fk_error.png)

------

### Reference

* [打包到appstore出现的问题](http://www.jianshu.com/p/91ac96a0f445)，后面遇到作为一个参考
* [更多的一些注意事项](http://www.jianshu.com/p/87dbf57cfe4a)
* [bitcode错误](http://blog.csdn.net/dp948080952/article/details/52749120)，打包的时候可能会遇到，后期参考



