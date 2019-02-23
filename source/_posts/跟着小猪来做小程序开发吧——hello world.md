---
title: 跟着小猪来做小程序开发吧——hello world
tags:
  - wechat
  - 小程序
  - 微信
id: 1181
comment: false
categories:
  - 微信开发
date: 2017-04-07 15:19:43
---

前期的准备工作我们已经准备好了，接下来就要进入开发阶段了。首先来进行一些必要的设置。

申请好小程序之后，我们需要到[微信后台](https://mp.weixin.qq.com)查看我们已经申请好的appid。如下图
![](http://wx4.sinaimg.cn/mw690/88e12591gy1fedxgl4df1j218o0ywdjj.jpg)

到本地打开《微信web开发者工具》，点击’本地小程序项目‘=》'添加项目'=》填写从微信后台得到的appid以及本地的一些目录信息之后，就可以开始本地开发调试了。
![](http://wx1.sinaimg.cn/mw690/88e12591gy1fedxth39xcj20xc0qo762.jpg)
![](http://wx3.sinaimg.cn/mw690/88e12591gy1fedxti44yaj21kw0voadc.jpg)

我们添加了合适的appid之后，开发者工具会自动从他们的服务器端拉下该小程序的配置信息，如下图。
![](http://wx3.sinaimg.cn/mw690/88e12591gy1fedxtj9zpnj21kw0vojuf.jpg)

并且在新增项目之后，如果点选了创建`quick start代码`复选框之后，web开发者工具还会为开发者提供了一个hello world的程序。我们可以直接运行该demo来查看效果。也可以通过该demo来了解小程序开发时项目文件结构。

直接运行demo程序就可以看到我们的hello world程序了。
![](http://wx3.sinaimg.cn/mw690/88e12591gy1fedycyx2qsj21kw0vo7bx.jpg)
点击授权之后进入到demo的主界面。
![](http://wx2.sinaimg.cn/mw690/88e12591gy1fedyeitva1j21kw0von4f.jpg)

这篇文章就介绍到这里，主要涉及到一些基本开发配置。到目前为止我们还没有写什么业务逻辑代码。

然后需要看看[官方文档](https://mp.weixin.qq.com/debug/wxadoc/dev/)中关于小程序文件结构的说明。

接下来的一篇小猪将和大家一起来完成一个简单的程序功能。即使用小程序来获取当前微信用户的用户信息。