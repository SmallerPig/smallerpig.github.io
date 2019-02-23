---
title: 让hexo的首页只显示文章的部分内容而不是全部
id: set-hexo-show-more-button-on-indexpage
categories:
  - WEB开发
date: 2017-09-30 11:01:40
tags:
    - blog
    - hexo
---


Hexo 的 Next 主题默认是首页显示你每篇文章的全文内容，那么要如何设置只显示部分呢？正如你现在看到的本篇文章，只显示到这里。

<!-- more -->

目标

在网站首页只显示每篇文章的部分内容，不要全部内容都展示出来。

解决

要解决这个问题有两个方法：一是修改 主题 _config.yml 文件设置，而是直接在你的 md 中加一句代码即可。

## 第一种方法

用文本编辑器打开 themes/ 目录下的对应的主题的theme文件夹下的 _config.yml 文件，找到这段代码，如果没有则新建，可能不同的主题会不支持这种方法：

```
# Automatically Excerpt. Not recommend.
# Please use <!-- more --> in the post to control excerpt accurately.
auto_excerpt:
  enable: false
  length: 150

```
把 enable 的 false 改成 true 就行了，然后 length 是设定文章预览的文本长度。

修改后重启 hexo 就ok了。

## 第二种方法

在你写 md 文章的时候，可以在内容中加上 `<!--more-->`，这样首页和列表页展示的文章内容就是 `<!--more-->` 之前的文字，而之后的就不会显示了。

效果

上面两种方式展示出来的效果是不一样的。

第一种修改 _config.yml 文件的效果是会格式化你文章的样式，直接把文字挤在一起显示，最后会有 ...。



而第二种加上 `<!--more--> `展示出来的就是你原本文章的样式，最后不会有...。


## 第三种方式

在文章的 `front-matter` 中添加 `description`，并提供文章摘录

```
---
title: 让hexo的首页只显示文章的部分内容而不是全部
id: set-hexo-show-more-button-on-index
categories:
  - WEB开发
date: 2017-09-30 11:01:40
tags:
    - blog
    - hexo
description: Hexo 的 Next 主题默认是首页显示你每篇文章的全文内容，那么要如何设置只显示部分呢？正如你现在看到的本篇文章，只显示到这里。
---
```
但是使用这种方式生成的描述信息在文章的详情页是不再显示的。

## 总结

各种方式展示的效果各有好处，第二种方法保留了样式而且可以自行选择显示哪些内容来预览，推荐使用此方法，第一种方法显示的每篇文章的预览都是一样的高度，第三种则需要在文章的[front-matter]{https://hexo.io/docs/front-matter.html}里面添加。

综合考虑的话还是建议使用第二种方法，毕竟以后各种插件也能准确的获取到你想要输出的本篇的描述信息。

参考链接：
- http://www.5isjyx.com/coding/201704/nextreadthefulltext.html
- http://theme-next.iissnan.com/faqs.html