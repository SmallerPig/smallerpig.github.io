---
title: 在wamp server环境下安装php_imagick拓展
id: 673
categories:
  - PHP
  - WEB开发
date: 2015-05-07 15:54:30
tags:
---

一般我们在安装php拓展会很简单.直接三部就可以搞定.!

1.  将下载好的dll文件放到php文件夹的ext文件夹下.  <li>编辑php.ini文件来加载之前的dll:extension:php_XXX.dll  <li>重启Apache 

这样我们就可以使用该拓展了.

但是安装php_imagick这类拓展时会稍微麻烦一点,因为其拓展库已经不再是一个简单的dll文件了…

下面跟随我的脚步来一步一步实现在wamp server环境下安装php_imagick拓展吧(其实其他环境是类似的!)

1.在php安装目录的ext文件夹下新建imagick文件夹

2.将该文件夹的路径添加到系统路径(path值).

3.从网址[http://windows.php.net/downloads/pecl/releases/imagick/](http://windows.php.net/downloads/pecl/releases/imagick/)下载对应php版本的imagick

4.这里需要注意的是下载的文件区别,我们从文件名中来区分,例如php_imagick-3.2.0b2-5.5-nts-vc11-x86.zip

*   其中5.5是对应的php版本.
*   nts值代表该文件适用于IIS和windows,ts代表该文件适用于Apache,
*   VC11和VC9是编译器的版本.我们可以适用phpinfo\(\)命令来查看我们机器上php适用的是哪个版本(如图1)
*   x86代表适用32位系统,x64代表适用64位系统 

5.下载好对应的zip文件后,将所有文件解压到我们第一步新建的imagick文件夹!

6.添加php_imagick.dll的完整路径到php.ini文件中.例如:extension=C:\wamp\bin\php\php5.5.12\ext\ext\imagick\php_imagick.dll

7.重启wamp服务器

8.运行phpinfo看看是不是已经成功添加拓展了

[![image](http://www.smallerpig.com/wp-content/uploads/2015/05/image_thumb1.png "image")](http://www.smallerpig.com/wp-content/uploads/2015/05/image1.png)

图1:使用phpinfo命令查看VC版本

[![image](http://www.smallerpig.com/wp-content/uploads/2015/05/image_thumb2.png "image")](http://www.smallerpig.com/wp-content/uploads/2015/05/image2.png)

图2:安装成功后使用phpinfo查看imagick信息

参考:[http://stackoverflow.com/questions/25766737/where-to-find-php-imagick-dll-for-php-5-5-12-for-windows-wampserver-2-5](http://stackoverflow.com/questions/25766737/where-to-find-php-imagick-dll-for-php-5-5-12-for-windows-wampserver-2-5 "http://stackoverflow.com/questions/25766737/where-to-find-php-imagick-dll-for-php-5-5-12-for-windows-wampserver-2-5")