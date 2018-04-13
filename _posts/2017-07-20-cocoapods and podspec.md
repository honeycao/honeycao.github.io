---
layout: post
title: CocoaPods支持导入自己的开源库（一）
date: 2017-07-20
tag: CocoaPods
---

![CocoaPods](/images/posts/cocoapods/CocoaPods.jpg)

### 目录
* [前提](#前提)
* [创建podspec文件](#创建podspec文件)
* [podspec文件书写](#podspec文件书写)
* [验证 podspec 文件](#验证 podspec 文件)
    * [打标签](#打标签)
    * [注册](#注册)
    * [验证](#验证)
* [推送 podspec 文件到cocoapods](#推送)
* [更新 podspec 文件](#更新)
* [搜索库](#搜索)
* [错误集中处理](#错误集中处理)

------

### <a id="前提"></a>前提
* 有`cocoapods`的基本环境（安装过`cocoapods`）
* `github`上有相应的开源代码库，或者新建一个代码库
  clone到本地来操作

    ```
    //clone到本地
    $ git clone https://github.com/honeycao/CocoaPodDemo.git
    //后续操作都是基于在项目目录下执行
    $ cd CocoaPodDemo   
    ```

------

### <a id="创建podspec文件"></a>创建podspec文件
为了让自己的开源库支持`cocoapods`导入，关键就是`podspec`文件

```
//下面2个命令功能是一样的，都是在当前目录创建一个 cocoapodDemo.podspec 文件
$ pod spec create cocoapodDemo
$ pod spec create cocoapodDemo.podspec
```

### <a id="podspec文件书写"></a>podspec文件书写
在书写`podspec`文件之前，我们的开源代码库需要有一个规范的目录结构（一般包括源码 + demo），也是为了方便后面源码路径的选择

**开源代码库目录结构**

```
.
├── cocoapodDemo（主文件夹）
    ├── cocoapodDemo（源码）
        ├── xxx.bundle
        ├── xxx.h/m
    ├── cocoapodDemoExample（demo示例）
    ├── cocoapodDemoExample.xcodeproj
    ├── cocoapod.podspec
    ├── README.md
```

**podspec文件简单模板**

注意：在书写的时候最好不要用文本编辑器打开，容易造成标点符号中英文混用的错误，最好用vim或者sublime打开。

```
Pod::Spec.new do |s|

  s.name         = "CocoaPodDemo"
  s.version      = "1.0.0"
  s.summary      = "summary"
  s.description  = "description"
  s.homepage     = "https://github.com/honeycao/CocoaPodDemo"
  #s.screenshots  = "www.example.com/screenshots_1.gif", "www.example.com/screenshots_2.gif"

  s.license      = "MIT"
  s.author       = { "author" => "author@example.com" }
  s.platform     = :ios, "7.0"

  s.source       = { :git => "http://github.com/honeycao/cocoapod.git", :tag => "#{s.version}" }
  s.source_files  = "CocoaPodDemo/*.{h,m}"
  s.resources = "CocoaPodDemo/CocoaPodDemo.bundle"

  s.requires_arc = true
  # s.dependency "ACAletController", "~> 1.0.0"

end
```

`s.version`：版本号，每次更新必定修改的内容<br/>
`s.author`：作者以及邮箱<br/>
`s.platform`：平台以及最低版本限制<br/>
`s.source`：源代码的一个路径，这里指向的github上对应的某一个版本，而版本号就是和该文件版本相关联，所以这里要确保github上存在这个版本号，也就是验证之前的打标签<br/>
`s.source_files`：资源文件路径<br/>
`s.dependency`：依赖库，有时候代码会依赖于其他第三方，需要在这里配置

------

### <a id="验证 podspec 文件"></a>验证 podspec 文件

#### <a id="打标签"></a>打标签
podspec 中定义的版本号，在开源库中如果没有对应的标签，那么我们在验证之前就需要打个标签

```
$ git tag 1.0.0     #给当前工作副本节点当上 1.0.0 标签
$ git push --tags   #推送所有标签到远端 

```

#### <a id="注册"></a>注册
对于一个新的开源库第一次添加`cocoapods`支持，是需要进行注册的，而且会有邮件进行确认

```
// 邮箱 以及 名称 进行注册
$ pod trunk register author@example.com 'author'

//注册成功后可以查看个人信息
$ pod trunk me
```

#### <a id="验证"></a>验证

* 直接验证
    ```
    $ pod spec lint
    ```

* 忽略警告（有时候有无法避免的警告，可以忽略警告）
    ```
    $ pod spec lint --allow-warnings
    ```

* 显示错误（如果验证出错，可以采用这个命令显示出更清晰的错误信息）
    ```
    $ pod spec lint --verbose
    ```

* 验证通过显示结果
    ```
     -> CocoaPodDemo (1.0.0)

    Analyzed 1 podspec.

    CocoaPodDemo.podspec passed validation.
    ```

------

### <a id="推送"></a>推送 podspec 文件到cocoapods
推送的过程中会再次验证一遍，所以有时候有会出现验证不通过的情况

```
$ pod trunk push CocoaPodDemo.podspec
```
如果在验证的时候添加了忽略警告命令，那么在推送的时候也必须加上，不然前面的警告会再次出现导致验证又通不过。
```
$ pod trunk push CocoaPodDemo.podspec --allow-warnings
```
成功之后显示

![trunk_push_success](/images/posts/cocoapods/trunk_push_success.png)

------

### <a id="更新"></a>更新 podspec 文件
由于我们的开源库版本更新，所以我们的 podspec 文件也必须更新，至于更新步骤就是重复上面步骤

* 修改`podspec`文件中`s.version`对应的版本号
* 验证
* 给我们开源库打上标签（tag 和 s.version是一致的）
* 推送

------

### <a id="搜索"></a>搜索库
一般推送成功之后，我们并不能直接搜索到我们的库，具体怎么做，需要分为两种情况

* **首次推送成功**：需先删除本地缓存再进行搜索
    ```
    $ rm ~/Library/Caches/CocoaPods/search_index.json
    $ pod search CocoaPodDemo
    ```

* **podspec文件更新至推送成功**：删除本地缓存进行搜索查看到的也只是旧版本，这时候只需更新pod即可搜索到最新版本的库
    ```
    $ pod repo update
    $ pod search CocoaPodDemo
    ```

------

### <a id="错误集中处理"></a>错误集中处理

**1、验证过程中出现根据路径找不到匹配文件等如下情况**
```
- ERROR | [iOS] file patterns: The `resources` pattern did not match any file.
- - ERROR | [iOS] file patterns: The `source_files` pattern did not match any file.
```

我们可以到 `podspec` 验证路径下去手动修改

* `podspec` 本地验证路径：`~/Library/Caches/CocoaPods/Pods/External`
* 在该路径下可以找到我们验证的代码库，进入子目录下修改文件路径，或者如果是资源文件不存在，也可以直接添加进去即可
* 再次执行验证应该就没有这个问题了



