---
title: 解决windows下JetBrains系列IDE的终端命令行工具无法输入的问题
tags:
  - IDE
  - jetbrains
  - terminal
id: 926
categories:
  - IDE
date: 2015-10-31 11:30:57
---

JetBrains系的IDE是小猪除了VS使用最多的IDE了，包括phpstrom， pycharm，Idea等等。但是只从使用了windows10之后其中非方便的一个功能却用不了了，就是可以直接在编辑器内部的终端(terminal)直接输入命令来执行的窗口却无法输入了。
今天小猪来分享怎么解决这个问题。
1\. 打开cmd。按win+R
2\. 在打开的运行框中输入CMD。您将看到系统的命令行工具
3\. 右击弹出的系统命令行工具左上角的c盘的小图标并在菜单中选择"属性"
4\. 勾选上“使用旧版控制台(需要重新启动)”并确定

完成后重启IDE就可以啦。
原文地址：http://www.smallerpig.com/926.html
转载注明出处哦：