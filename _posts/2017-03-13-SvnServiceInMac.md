---
layout:     post
title:      Mac上搭建本地svn服务器
subtitle:   
date:       2017-03-13
author:     "AllenCao"
header-img: "img/post-bg-3.png"
catalog: true
tags:
    - svn
---

### 一、创建`svn repository`
>在终端输入创建命令，后面是创建的路径（不用非要在当前文件夹路径中执行
>如果`TestCode`不存在，那么会自定创建。


```
$ svnadmin create /Users/AllenCao/svn/TestCode
```
* 执行之后文件夹目录结构

![svnService_1](/img/in-post/svnService/svnService_1.png)

---
### 二、配置svn的用户权限
#### 打开 svnserve.conf，将下列配置项前面的#和空格都去掉
```
# anon-access = read
# auth-access = write
# password-db = passwd
# authz-db = authz
```
![svnService_2](/img/in-post/svnService/svnService_2.png)

同时再将 anon-access = read 改为 anon-access = none，这样禁止匿名访问，需要帐号密码才能访问



#### 打开passwd，在[users]下面添加帐号和密码
![svnService_3](/img/in-post/svnService/svnService_3.png)
这里同时创建了2个账号<br>
账号是guest, 密码123<br>
账号是coder，密码123<br>

#### 打开authz，配置用户组和权限

![svnService_4](/img/in-post/svnService/svnService_4.png)

PS：这里设置的是群组权限，rw代表读写属性，如果我们上面只创建了一个用户，那么只要直接设置这个用户权限；这个[/]，代表的是当前根目录，意味着拥有这个目录的权限

```
[/]
coder=rw
```

#### 启动SVN服务
>执行命令的时候路径不用到创建的时候的最底层路径，这点注意一下。
>成功之后可以用活动监视器查看


```
$ svnserve -d -r /Users/AllenCao/svn 
```
![svnService_5](/img/in-post/svnService/svnService_5.png)

---
### 三、使用svn客户端功能
#### 从本地导入代码到服务器(第一次初始化导入)在终端中输入
```
$ svn import /Users/AllenCao/Desktop/Coding svn://localhost/TestCode/tags --username=coder --password=123 -m "初始化导入"
```
PS：将 本地`/Users/AllenCao/Desktop/Coding` 中的所有内容，上传到本地服务器 `TestCode` 仓库的 `tags` 目录下，后面双引号中的"初始化导入"是注释

----------
#### 从服务器端下载代码到客户端本地
```
svn checkout svn://localhost/TestCode /Users/AllenCao/Desktop/receiveCode
//由于第一步输入了账号密码，所以不用输入，如果输入的话位置是放在两个路径之间的位置
svn checkout svn://localhost/TestCode --username=coder --password=123 /Users/AllenCao/Desktop/receiveCode
```
PS：将本地服务器中 TestCode 仓库的内容下载到 本地`/Users/AllenCao/Desktop/receiveCode` 目录中，那么在`receiveCode`文件夹中可以看到有内容了。
![svnService_6](/img/in-post/svnService/svnService_6.png)

----------
#### 接着就是一些svn常用命令了
>执行这些操作的时候都需要到本地文件夹路径中，如当前就该是`/Users/AllenCao/Desktop/receiveCode`路径下执行操作

* `svn commit -m "提交备注"`   ---提交本地文件到服务器
* `svn update`                 ---更新服务器端的代码到客户端
* `svn help`                   ----帮助

---

### 四、后续改进
#### 搭建完成如何给他人使用呢？
> 由于是本地服务器，所以只限于同一局域网，也就是同一网络下就行，自己的svn地址是：svn://localhost/TestCode ，那么他人的就是svn://svn服务器地址（比如说我现在电脑的地址是192.168.1.110，svn地址就是svn://192.168.1.1110/TestCode）

#### 在同一个仓库下，vesion会叠加，不会因为是2个项目
>什么情况呢？就是在`svn/Test` 这个仓库下，同时导入两个项目，那么版本修改的vesion是共同的，不是独立的。
如果要独立，就只有在两个仓库下，`svn/Test1` `svn/Test2`

#### `svn import` 的时候不能够提交`.a`文件
>在一般的第三方SDK上会有这个.a文件，所以必须第一次导入的时候需要成功上传，系统自动是屏蔽掉.a的，可以在终端输入命令用vim查看 `vi ~/.subversion/config`

```
// 可以看到以下行被屏蔽，而且其中包括.a，我们先删除 *.a，然后再取消注释
#global-ignores = *.o *.lo *.la *.al .libs *.so *.so.[0-9]* *.a *.pyc *.pyo # *.rej *~ #*# .#* .*.swp .DS_Store

//这就是最后的样子
global-ignores = *.o *.lo *.la *.al .libs *.so *.so.[0-9]* *.pyc *.pyo *.rej *~ #*# .#* .*.swp .DS_Store
```
经过上面的步骤之后，就再也不会屏蔽.a，了，一劳永逸，假如有其他需求，只要到这个文件中来修改就行。

#### `svn log`查看日志出现`Item is not readable`

* 解决：`svnserve.conf` 文件中,设置`anon-access = none`就可以了.