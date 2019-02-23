---
title: Visual Studio 解决Windows Web服务框架中出现了无法识别的错误
id: 291
categories:
  - WEB开发
date: 2013-08-23 17:16:13
tags:
---

使用的是Visual Studio2012 ULT 控制台项目按ctrl+F5可以运行，不可以直接按F5调试，出现“尝试运行项目时出错(项目地址)Windows Web服务框架中出现了无法识别的错误”。可是调试ASP.NET程序却没有问题。

![](http://www.smallerpig.com/wp-content/plugins/wp-ueditor/ueditor/php/upload/1771377249614.png "QQ截图20130823171351.png")

小猪遇到这个问题很是郁闷，不知道是什么地方出现了问题，百度了好久都没有个正式的解决方案，例如

[](http://bbs.csdn.net/topics/390511663)[http://bbs.csdn.net/topics/390511663](http://bbs.csdn.net/topics/390511663)

只好谷歌了一下。

[http://connect.microsoft.com/VisualStudio/feedback/details/781459/visual-studio-versucht-eine-remoteverbindung-herzustellen-wenn-32-bit-bevorzugen-deaktiviert-ist](http://connect.microsoft.com/VisualStudio/feedback/details/781459/visual-studio-versucht-eine-remoteverbindung-herzustellen-wenn-32-bit-bevorzugen-deaktiviert-ist)

看看人家的问题、人家微软的回家。让国内的一些XXX情何以堪呐！！ 

发现了某位大大这么回答。

This is happening because Visual Studio is a 32-bit application. &nbsp;Only 64-bit applications are able to debug other 64-bit applications, so Visual Studio creates a fake remote connection to a 64-bit debugger when debugging 64-bit apps. &nbsp;If you select the prefer 32-bit option, then this is unnecessary.

虽然小猪英文不太好，但是看到32-bit、64-bit也就大差不差的了解人家在说什么了。小猪这才恍然大悟，可能是系统装的是64位系统，某些指令集支持的不够好。

 按照这个提示，把需要调试的程序改成X86。

![](http://www.smallerpig.com/wp-content/plugins/wp-ueditor/ueditor/php/upload/39891377249614.jpg "QQ图片20130823171439.jpg")

再点调试，……完美运行……

另外有些同志说需要把类似防火墙的东东关掉，这个我就不太清楚了。