---
title: Windows下安装mysqlclient
id: 795
categories:
  - Django
  - python
date: 2015-07-06 23:37:02
tags:
---

由于使用Python3下的Django. 原来的MySQLdb驱动已经不支持.

引用Django官网上一段话:At the time of writing, the latest release of MySQLdb (1.2.5) doesn’t support Python 3\. In order to use MySQLdb under Python 3, you’ll have to install <tt>mysqlclient</tt> instead.

所以得安装mysqlclient了!

这里我们使用pip来安装!

先在命令行里输入pip看看是否成功安装pip.如果Windows提示未找到命令，可以重新运行安装程序添加`pip`。

正在安装过程中:

[![image](http://www.smallerpig.com/wp-content/uploads/2015/07/image_thumb.png "image")](http://www.smallerpig.com/wp-content/uploads/2015/07/image.png)

经过漫长的等待之后终于安装成功.,期间视网络情况可能会出现多次下载失败的情况!

[![image](http://www.smallerpig.com/wp-content/uploads/2015/07/image_thumb1.png "image")](http://www.smallerpig.com/wp-content/uploads/2015/07/image1.png)

如果多次下载失败后我们手动到官网上下载whl文件

mysqlclient官网下载地址:[http://dev.mysql.com/downloads/connector/python/](http://dev.mysql.com/downloads/connector/python/ "http://dev.mysql.com/downloads/connector/python/")

然后在命令行工具中先安装wheel工具:

pip install wheel

通常这里会很快安装好wheel,然后找到你要安装的whl文件，在命令行下执行 wheel install XXXXX-none-any.whl(这里的文件名可能不同)