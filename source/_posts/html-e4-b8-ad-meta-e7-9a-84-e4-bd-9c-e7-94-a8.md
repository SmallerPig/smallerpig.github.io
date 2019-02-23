---
title: HTML 中 META的作用
id: 390
categories:
  - HTML
date: 2014-01-14 10:22:08
tags:
---

```
## 

说明：

	meta是用来在HTML文档中模拟HTTP协议的响应头报文。meta 标签用于网页的<head>与</head>中，meta 标签的用处很多。meta 的属性有两种：name和http-equiv。name属性主要用于描述网页，对应于content（网页内容），以便于搜索引擎机器人查找、分类（目前几乎所有的搜索引擎都使用网上机器人自动查找meta值来给网页分类）。这其中最重要的是description（站点在搜索引擎上的描述）和keywords（分类关键词），所以应该给每页加一个meta值。比较常用的有以下几个：

```
## 

name 属性

	1、<meta name="Generator" contect="">用以说明生成工具（如Microsoft FrontPage 4.0）等；

	2、<meta name="KEYWords" contect="">向搜索引擎说明你的网页的关键词；

	3、<meta name="DEscription" contect="">告诉搜索引擎你的站点的主要内容；

	4、<meta name="Author" contect="你的姓名">告诉搜索引擎你的站点的制作的作者；

	5、<meta name="Robots" contect= "all|none|index|noindex|follow|nofollow">

	其中的属性说明如下：

*   设定为all：文件将被检索，且页面上的链接可以被查询；
*   设定为none：文件将不被检索，且页面上的链接不可以被查询；
*   设定为index：文件将被检索；
*   设定为follow：页面上的链接可以被查询；
*   设定为noindex：文件将不被检索，但页面上的链接可以被查询；
*   设定为nofollow：文件将不被检索，页面上的链接可以被查询。

```
## 

http-equiv属性

	1、<meta http-equiv="Content-Type" contect="text/html";charset=gb_2312-80">

	和 <meta http-equiv="Content-Language" contect="zh-CN">用以说明主页制作所使用的文字以及语言；

	又如英文是ISO-8859-1字符集，还有BIG5、utf-8、shift-Jis、Euc、Koi8-2等字符集；

	2、<meta http-equiv="Refresh" contect="n;url=http://yourlink">定时让网页在指定的时间n内，跳转到页面http://yourlink；

	3、<meta http-equiv="Expires" contect="Mon,12 May 2001 00:20:00 GMT">可以用于设定网页的到期时间，一旦过期则必须到服务器上重新调用。需要注意的是必须使用GMT时间格式；

	4、<meta http-equiv="Pragma" contect="no-cache">是用于设定禁止浏览器从本地机的缓存中调阅页面内容，设定后一旦离开网页就无法从Cache中再调出；

	5、<meta http-equiv="set-cookie" contect="Mon,12 May 2001 00:20:00 GMT">cookie设定，如果网页过期，存盘的cookie将被删除。需要注意的也是必须使用GMT时间格式；

	6、<meta http-equiv="Pics-label" contect="">网页等级评定，在IE的internet选项中有一项内容设置，可以防止浏览一些受限制的网站，而网站的限制级别就是通过meta属性来设置的；

	7、<meta http-equiv="windows-Target" contect="_top">强制页面在当前窗口中以独立页面显示，可以防止自己的网页被别人当作一个frame页调用；

	8、<meta http-equiv="Page-Enter" contect="revealTrans(duration=10,transtion= 50)">和<meta http-equiv="Page-Exit" contect="revealTrans(duration=20，transtion=6)">设定进入和离开页面时的特殊效果，这个功能即FrontPage中的'格式/网页过渡';，不过所加的页面不能够是一个frame页面。

	以上是常用的几个meta属性，有个人主页的朋友不妨在你的主页中加上它，效果可是不一样的哦：）。

```
## 

什么是Viewport

	手机浏览器是把页面放在一个虚拟的'窗口';（viewport）中，通常这个虚拟的'窗口';（viewport）比屏幕宽，这样就不用把每个网页挤到很小的窗口中（这样会破坏没有针对手机浏览器优化的网页的布局），用户可以通过平移和缩放来看网页的不同部分。移动版的 Safari 浏览器最新引进了 viewport 这个 meta tag，让网页开发者来控制 viewport 的大小和缩放，其他手机浏览器也基本支持。

```
## 

Viewport 基础

	一个常用的针对移动网页优化过的页面的 viewport meta 标签大致如下：

	<meta name=';viewport'; content=';width=device-width, initial-scale=1, maximum-scale=1&Prime;>

*   <span style="font-size: 13px; line-height: 1.6em;">width：控制 viewport 的大小，可以指定的一个值，如果 600，或者特殊的值，如 device-width 为设备的宽度（单位为缩放为 100% 时的 CSS 的像素）。</span>
*   <span style="font-size: 13px; line-height: 1.6em;">height：和 width 相对应，指定高度。</span>
*   initial-scale：初始缩放比例，也即是当页面第一次 load 的时候缩放比例。
*   maximum-scale：允许用户缩放到的最大比例。
*   minimum-scale：允许用户缩放到的最小比例。
*   user-scalable：用户是否可以手动缩放