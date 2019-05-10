**前往预览**：千虑必有一得  [个人博客]( https://honeycao.github.io) 


#### 快速体验

1. 下载到本地
2. 进入到目录下
3. 然后执行如下命令，调起本地服务
```
$ jekyll serve      //开启服务
$ ctrl/control + c  //停止服务
```
4. 在浏览器中输入：`http://127.0.0.1:4000`
5. 修改config文件需要重启服务，其余的修改基本可以及时刷新

---

#### 目录解读+快速上手

对于需要修改的后面添加了备注，其余的都是不用修改。

```
+ _includes
	- featured-tags.html
	- footer.html  (修改链接的跳转)
	- friends.html
	- head.html
	- intro-header.html
	- mathjax_support.html
	- nav.html
	- short-about.html
	- sns-links.html
+ _layouts
	- defaults.html
	- keynote.html
	- page.html
	- post.html (相比原型去掉了标题前的#符号)
+ _posts （文章区，存放个人自己的文章，记得修改，注意文章的格式）
+ css
+ fonts
+ js
+ less
+ img (图片区，个人使用到的图片，记得修改，注意个别图片名字是不可改变)
+ pwa 
	+ icons (图标区，修改为与自己相关的信息)
	- manifest.json (修改为与自己相关的信息)
+ Extension (下面单独备注)
- _config.yml （全局的一些配置信息，对于个人使用的话都需要手动修改）
- 404.html
- about.html (关于自己，记得修改)
- archive.html 
- CNAME (域名，记得修改)
- feed.xml
- Gruntfile.js
- index.html
- offline.html
- package.json  (改不改不影响)
- sw.js

```

**备注**：Extension文件夹用于存放拓展的导航栏入口

默认的导航栏右上角入口为：Home、About、Archive

在Extension直接添加html文件，可直接在Archive后面显示，作为一个拓展功能的入口

**要求**：在对应的html文件顶部添加如下标题内容

```
---
title: jianli
layout: default
---
```

---

#### 参考

初始博客模板来自 [HUX](https://github.com/Huxpro/huxblog-boilerplate)，特别感谢。
自己在此基础上作了简单的一个修改，方便后续使用。