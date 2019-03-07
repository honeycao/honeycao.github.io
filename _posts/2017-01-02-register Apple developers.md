---
layout:     post
title:      注册苹果开发者账号完整流程
date:       2017-01-02
author:     "caoyq"
header-img: "img/post-bg-1.jpg"
catalog: true
tags:
    - iOS
    - 开发者账号
---


### 前言
>对于开发者账号申请，网上资料很多，自己写的这个，一方面是基于最新的注册方式，也就是网上搜到的有些太久远和现在不大一致；另一方面：不同人注册，可能遇到不同问题，所以自己也记录下其中遇到的坑，方便后人进行参考。

### 开发者基本知识

#### 1、什么是开发者账号
发布应用到AppStore上必须的的账号，本身都是一个Apple ID，注册APPID是免费的，当然这个Apple ID现在也可以用来做最简单的真机测试，以前是不可以的，后来改了，这样开发者在测试的时候就不必要都用开发者账号，Apple ID和开发者账号的关系，其实就是一个普通QQ和开了会员的QQ一样，后者都是需要付费的，当然注册开发者那过程就比开会员复杂多了。

#### 2、开发者账号的类型
[开发者官网对开发者账号的说明以及权限](https://developer.apple.com/support/compare-memberships/cn/)
* 个人： $99
* 公司/组织：$99
* 企业：$299，很少用到，因为不能上架AppStore，只能大型企业内部测试专用。

#### 3、注册一个Apple ID
* [注册Apple ID](https://appleid.apple.com/account#!&page=create)
* 注意事项：
    * 国籍选好中国，避免后面开发者账号注册的时候带来不必要的麻烦（虽然不知道具体有什么麻烦）
    * 年龄一定要满18岁，这个也会是后面注册开发者账号失败的原因。

![注册开发者账号——Apple ID注册年龄小于18岁.png](http://upload-images.jianshu.io/upload_images/1896558-777682c626695a8f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

--------

### 开发者账号的注册
>个人开发者账号是不需要邓白氏编码(D-U-N-S)，而公司和企业账号都是需要的

#### 1、免费申请企业的邓白氏编码(D-U-N-S)
[申请公司的D-U-N-S](https://developer.apple.com/enroll/duns-lookup/#/search)

* **邓白氏编码申请——公司组织信息填写**

![邓白氏编码申请——公司组织信息填写.png](http://upload-images.jianshu.io/upload_images/1896558-162ae9f5a208dab8.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

* **邓白氏编码申请——申请人信息填写**

![邓白氏编码申请——当前申请人信息.png](http://upload-images.jianshu.io/upload_images/1896558-0e61c098353458d7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

* **结果：**
    * 如果公司已经申请过DUNS，那么我们的填写的邮箱会很快收到发送过来的DUNS（DUNS是由9位数字组成）
    * 如果没有申请过，那么会让你确认填写信息，然后提交到邓白氏公司，后续会收到邮件告知，申请已经收到，近期会联系我们确认提交的信息，时间差不多一周（由于我申请的时候，公司有了，所以没到这一步，所以时间只是参考别人的，不知道最新是否时间有所改动）
    * 拿到DUNS，邓白氏公司最多需要14个工作日才能将更新后的信息提交给Apple，所以耐心等候再去进行注册环节。（时间不一致，具体看个人，可以先去注册看看，注册失败可能就是由于没有更新到位，那么久继续等候）

--------

#### 2、公司级开发者账号申请
[开发者账号注册](https://developer.apple.com/programs/enroll/cn/)

* **选择你要注册的开发者账号类型**

![注册开发账号——账号类型.png](http://upload-images.jianshu.io/upload_images/1896558-66ff50dab8218952.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

* **公司法人信息填写**

![注册开发者账号——法人信息填写.png](http://upload-images.jianshu.io/upload_images/1896558-ecf7797e954b797f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

* **公司信息填写**

![注册开发者账号——公司信息填写.png](http://upload-images.jianshu.io/upload_images/1896558-68a2db52cd17b5ee.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


* **注册进入审核阶段**

> 注册进入审核会得到一个编号，后续苹果公司会与我们联系，这个应该就只需要1-2个工作日就可以收到邮件或电话进行确认，前提是没有其他错误的情况，有问题可以直接打电话和苹果开发者中心的电话进行沟通，提供自己的案例编号即可。

![注册开发者账号——注册进入审核.png](http://upload-images.jianshu.io/upload_images/1896558-1b86615968ab732f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

* **注册审核被通过，进行确认并进入付款**

>当联系完确认无误之后，会收到邮件告知注册请求已经通过，需要我们进行确认，也就点击邮件链接，勾选确认接受，然后就会可以进入付款环节了。

![注册开发者账号——注册审核通过.png](http://upload-images.jianshu.io/upload_images/1896558-29d569cdf6c70b72.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
![注册开发者账号——审核通过之后进行确认.png](http://upload-images.jianshu.io/upload_images/1896558-b368cebe9c2b6188.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
![注册开发者账号——审核通过之后进行付款.png](http://upload-images.jianshu.io/upload_images/1896558-1d9eb2039191ea92.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

* **遇到的错误**

    * 公司名和提供的DUNS不一致，仔细确认公司名（我自己申请DUNS的时候公司名没有 Tech，注册的时候有，这个找了好久）
![注册开发者账号——公司名和DUNS不一致.png](http://upload-images.jianshu.io/upload_images/1896558-879b8cd98c439362.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

    * 未知错误：可能就是邓白氏公司还没有将更新的信息提交到Apple，再等等就行；
![注册开发者账号——未知错误.png](http://upload-images.jianshu.io/upload_images/1896558-af51eeee96171e85.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


### 付款

>付款方式国内分为2种，反正都是信用卡支付，分别是`VISA`和`MasterCard`，具体的可以看信用卡，有这两者之一的标记的信用卡才行。


### Reference
* [苹果企业级开发者账号申请流程](https://club.oneapm.com/t/topic/1123)
* [苹果iOS开发者账号类型总结](http://www.cnblogs.com/KingStar/p/3642473.html)


### 写在后面的话
* 苹果开发者中心办公时间北京时间周一至周五，`09：00` 至 `17：00`，电话号码：`4006-701-855`
* 邓白氏公司咨询电话：`02126107475`、邮箱沟通：`appdeveloper@dnb.com`，在使用邮箱沟通时提供以下4个信息以方便查询：`个人联方式`、`公司信息`、`Request ID/DUNS(若无可跳过)`、`您的问题`。
* 注册相关时间可能不是很准确，因为自己没有太注意记录这个时间，所以只能作为一个参考
* 另外不知道你们平时登陆的苹果官网和开发者中心是中文还是英文的，反正我之前都是英文的，在注册开发者账号期间，有幸收到苹果开发者中心的邮件，发现里面链接全都是中文链接，研究发现其实就是在原有网址后面加上个`/cn`即可，如：`中文版开发中心` [https://developer.apple.com/cn/](https://developer.apple.com/cn/)，亏得我之前一直都不知道，不知道有多少小伙伴和我一样。。。
