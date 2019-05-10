---
layout:     post
title:      jekyll开启个人博客搭建之路
subtitle:   叫你如何使用jekyll + GitHub Page搭建最简单的博客
date:       2017-05-30
author:     "caoyq"
header-img: "img/post-bg-bookdesk.jpg"
catalog: true
tags:
    - blog
    - jekyll
---


### 说在前面的话

作为一个和电脑、代码打交道的我，一直都想拥有自己的博客，一切都显得那么高(zhuang)大(bi)上(yong)，在下定决心之后就在网上到处查找资料，最终发现一般用的就三种：`WordPress`、`Hexo`、`Jekyll`，最终权衡利弊选定了jekyll来实现博客搭建。

这个过程前前后后遇到很多坑，虽然有相关文档，毕竟总不够全面，在自己这里也有时候会出现一些他们没碰到的问题，总体比较艰辛，所以也要记录下自己搭建的过程，同时一直更新遇到的新问题，在使用过程中出现问题，可直接跳到末尾的`Q&A`进行查看是否有类似问题。

------

### jekyll介绍

`jekyll`是一个简单的免费的Blog生成工具，一个生成静态网页的工具，不需要数据库支持，只用 [Markdown](http://daringfireball.net/projects/markdown/) (或 [Textile](http://textile.sitemonks.com/))、[Liquid](http://wiki.shopify.com/Liquid)、HTML & CSS 就可以构建可部署的静态网站，最关键的是jekyll可以免费部署在Github上，而且可以绑定自己的域名。

至于更多详细信息可以直接查看相关文档：<a target="blank" href='http://jekyll.com.cn'>jekyll中
文文档</a>、<a target="blank" href='https://jekyllrb.com'>jekyll英文文档</a>

------

### jekyll安装

#### 1、事先准备

安装 `Jekyll` 相当简单，但是你得先做好一些准备工作 开始前你需要确保你在系统里已经有如下配置

* Git环境（部署到远端以及代码管理）
* <a target="http://www.ruby-lang.org/en/downloads">Ruby环境</a>（jekyll基于Ruby开发）
* 包管理器<a target="http://rubygems.org/pages/download">RubyGems</a>
* Linux, Unix, or Mac OS X（官方文档并不建议在Windows平台安装）
* Mac用户需要安装Xcode和Command-Line Tools，下载方式`Preferences → Downloads → Components`

#### 2、RubyGems 安装 Jekyll并启动

安装 jekyll

    $ gem install jekyll  

创建博客

    $ jekyll new myBlog 

进入博客目录

    $ cd myBlog  

启动本地服务

    $ jekyll serve

在浏览器里输入： <a target="blank" href='http://127.0.0.1:4000'>http://127.0.0.1:4000</a>，就可以看到你的博客效果了。

------

### 初版博客

#### 1、目录结构
基本jekyll网站启动后，本地目录结构如下，至于目录结构意义，可以参考 <a target="blank" href='http://jekyll.com.cn/docs/structure'>官方文档目录结构</a>

    .
    ├── _config.yml
    ├── _includes
    |   ├── footer.html
    |   └── header.html
    ├── _layouts
    |   ├── default.html
    |   ├── post.html
    |   └── page.html
    ├── _posts
    |   └── 2016-01-01-welcome-to-jekyll.markdown
    ├── _sass
    |   ├── _base.scss
    |   ├── _layout.scss
    |   └── _syntax-highlighting.scss
    ├── about.md
    ├── css
    |   └── main.scss
    ├── feed.xml
    └── index.html

看能力去自行修改对应文件，重新 `jekyll serve` 就可以看到效果，有些修改并不需要重新运行这个命令，具体的可尝试下就知道。

#### 2、编写博客文章

这里所有的博客文章都是放在 `_post` 目录下面

文章命名格式：`yyyy-mm-dd-title.md`，后缀可以是.md 或者 .markdown，如：`2016-01-02-firstBlog.md`

文章内容格式如下，其中每篇文章头部都是固定的

```
---
layout: post
title:  "firstBlog"
date:   2016-01-02 
categories: blog
---

正文...
```

`title`：文章标题；这里的标题和.md文章命名有一定区别，这里的标题是查看文章的时候显示在顶部，同时用中文也是没有任何问题的，而.md文章名是显示在网址中，也就是该文章的URL，所以作为url**最好不要使用中文，不然显示的就是乱码了**<br/>
`date`：日期<br/>
`categories`：标签；目前只能有一个标签<br/>

如果对`markdown`不熟悉，可参考另一篇文章 <a target="blank" href='/blog/MarkdownStudy.html'>markdown基本语法学习<br/>

**注意：模板是支持markdown语法，但不是支持全部语法，所以这个方面是有待改进。**

#### 3、部署到远端

这里是部署到`GitHub Page`，除了这个也可以部署到 `Gitlab`、`Coding`等，主要就是当做一个免费的服务器使用。

* github 上创建一个仓库，命名为 `username.github.io`，例如我的仓库就是 `honeycao.github.io`，这个是一个命名规范。
* 本地创建好的博客用git管理，然后推送到GitHub上（远程仓库中不需要`README.md`文件，本地需要新建一个`README.md`文件用于推送到远端）

        $ cd myBlog
        $ git init
        $ git add README.md    
        $ git commit -m "first commit"
        $ git remote add origin git@github.com:honeycao/test.git
        $ git push -u origin master

* 检测是否同步成功，成功之后再浏览器中输入 `username.github.io` 就可以访问该博客

------

### 博客模板

毕竟不是所有人都会`css`、`js`和`html`，所以有一个模板就显得很关键了，jekyll官方也提供了 <a target="blank" href='http://jekyllthemes.org/'>jekyll主题</a>，基于模板只需简单修改就可以使用了。<br/>
如果对于我博客模板有兴趣的，可以到我的 <a target="blank" href='https://github.com/honeycao/honeycao.github.io'>GitHub博客</a> 进行查看，或者下载下来运行看看，终端进入目录下，运行 `jekyll serve`，然后在浏览器里输入： <a target="blank" href='http://127.0.0.1:4000'>http://127.0.0.1:4000</a>，就可以看到博客效果了。

**注意**：由于我的博客使用了自定义的域名，所以第一次下载使用，需要修改域名，具体做法： 到目录下打开`CNAME`文件，清空我的域名信息即可，如果你有自己的域名，也可以将其修改为你的域名。<br/>

**如果想在我的博客基础上修改成你的博客，那么需要修改下面几个地方**

* 把 `_posts/` 目录下的文章都去掉。
* 把 `images/` 目录下图片替换，把 `img/in-post/`目录下 博客相关图片去掉
* 修改 `_config.yml` 文件里面的内容为你自己的。

------

### 拓展工具

>在我自己的博客中，除了模板自有功能之外，也添加了许多拓展的工具，如：评论、分享等，这里就介绍下我自己的经验，这些拓展工具并不统一，只是作为一个参考而已，仁者见仁智者见智，另外小伙伴们如果有更好的拓展工具也可以介绍给我，不胜感激。。。

#### 1、域名
如果没有自己的域名，那么博客网址就是 `username.github.io`，看起来总不是那么好，所以有兴趣就可以购买一个自己的域名玩玩，至于购买哪样的那看你自己的经济了；购买途径网上也有很多，这里也不多作介绍，主要说下域名解析的问题，一般购买域名的地方都可以免费解析，比如我是在 <a target="blank" href='https://cloud.baidu.com'>百度云</a> 买的，同时可以免费解析，如果没有地方解析，那么介绍一个可以免费解析的地方——<a target="blank" href='https://www.dnspod.cn'>DNSPOD</a>，不管在哪解析，解析步骤都是一样的，我自己在解析的时候就碰到一个问题，耽搁好久才解决，所以下面就贴出正确解析的方法

![域名解析](/img/in-post/jekyll/域名解析.png)

**主要注意记录值填写自己博客仓库名就行**

#### 2、数据统计

* <a target="blank" href='https://tongji.baidu.com'>百度统计</a>：统计网站访问详情，不过由于才疏学浅并不能导出统计数据，所以只是用来作为一个流量页面展示
* <a target="blank" href='http://busuanzi.ibruce.info'>不蒜子</a>：和百度统计相辅相成，这个只能用于获取统计数据，而并没有展示页面

#### 3、评论
由于多说6月初就停用了，所以也不考虑它。

* <a target="blank" href='https://changyan.kuaizhan.com'>畅言</a>：一开始想的是这个，但是由于自己的域名并没有备案，所以这个没法用
* <a target="blank" href='https://gentie.163.com'>网易云跟帖</a>：这个是目前自己用的，效果也很好，主要是并不需要备案。（网易云跟帖也出了最新通知，8月1号之后就没法用了）

#### 4、分享

* <a target="blank" href='http://share.baidu.com'>百度分享</a>：是目前所用的，目前自己用尚存在一个问题并没有解决：安装并成功分享之后，百度分享后台并没有获取到数据。。。

#### 5、404公益页面

一般的404页面没什么特色，偶然看到别人网站上404页面显示的是腾讯寻找丢失儿童的公益广告，感觉这个挺不错的，就借鉴过来用了；<br/>
至于用法就是把下面代码复制到博客目录下 `404.html` 文件中。可自行修改主页信息和链接。

    <script type="text/javascript" src="http://www.qq.com/404/search_children.js" charset="utf-8" homePageUrl="http://arthurcao.com" homePageName="ArthurCao的个人博客"></script>



------

### Refrerence

* [搭建一个免费的，无限流量的Blog----github Pages和Jekyll入门](http://www.ruanyifeng.com/blog/2012/08/blogging_with_jekyll.html)

* [Hux blog 模板](https://github.com/Huxpro/huxpro.github.io/blob/master/README.zh.md)

  ​

-------

### Q&A（持续更新）

>**错误是无法避免，我们能做的就是正视它**。<br/>
使用过程中遇到任何问题，可以联系我，或者在下面留言，我会尽力去解决，并在下面记录，或者你遇到了问题你自己已经解决也可以告诉我，目的就是为了让更多的人少走弯路。

**1、`gem install jekyll` 命令出错**

    $ sudo gem install jekyll
    ERROR:  While executing gem ... (OpenSSL::SSL::SSLError)
    hostname "upyun.gems.ruby-china.org" does not match the server certificate

原因：可能是证书错误或者需要更新Ruby环境（ruby版本必须是2.2以上）

* 解决证书错误

        $ sudo curl -O http://curl.haxx.se/ca/cacert.pem
        $ sudo mv cacert.pem cert.pem

* 更新Ruby环境

        0.安装homebrew，已经有了就可以跳过这一步
        /usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
        1.安装rvm$ curl -L get.rvm.io | bash -s stable
        2.装载rvm$ source ~/.rvm/scripts/rvm
        3.安装2.3.0版本ruby$ rvm install 2.3.0
        4.将2.3.0设为默认$ rvm use 2.3.0 --default


**2、本地推送到GitHub出错**

    $ git push -u origin master
    error: src refspec master does not match any.
    error: failed to push some refs to 'git@github.com:honeycao/username.github.io.git'

原因：创建远端仓库的时候，添加了一个`README.md`，本地推送到远端的时候没有`README.md`，那么需要先 pull 远端代码再进行推送

再或者远端也没有`README.md`，所以本地需要先新建一个，再进行推送

**3、使用我的博客，如果你本机没有任何jekyll环境，可能会报错**

    /Users/xxxxxxxx/.rvm/rubies/ruby-2.2.2/lib/ruby/site_ruby/2.2.0/rubygems/core_ext/kernel_require.rb:54:in `require': cannot load such file -- bundler (LoadError)
    from /Users/xxxxxxxx/.rvm/rubies/ruby-2.2.2/lib/ruby/site_ruby/2.2.0/rubygems/core_ext/kernel_require.rb:54:in `require'
    from /Users/xxxxxxxx/.rvm/gems/ruby-2.2.2/gems/jekyll-3.3.0/lib/jekyll/plugin_manager.rb:34:in `require_from_bundler'
    from /Users/xxxxxxxx/.rvm/gems/ruby-2.2.2/gems/jekyll-3.3.0/exe/jekyll:9:in `<top (required)>'
    from /Users/xxxxxxxx/.rvm/gems/ruby-2.2.2/bin/jekyll:23:in `load'
    from /Users/xxxxxxxx/.rvm/gems/ruby-2.2.2/bin/jekyll:23:in `<main>'
    from /Users/xxxxxxxx/.rvm/gems/ruby-2.2.2/bin/ruby_executable_hooks:15:in `eval'
    from /Users/xxxxxxxx/.rvm/gems/ruby-2.2.2/bin/ruby_executable_hooks:15:in `<main>'

原因：可能就是没有安装 bundler，执行安装 bundler 命名

    $ gem install bundler

成功之后再次执行`jekyll serve`，有一个新的错误提示

    Could not find proper version of jekyll (3.1.1) in any of the sources
    Run `bundle install` to install missing gems.

根据提示进行操作即可

    $ bundle install


















