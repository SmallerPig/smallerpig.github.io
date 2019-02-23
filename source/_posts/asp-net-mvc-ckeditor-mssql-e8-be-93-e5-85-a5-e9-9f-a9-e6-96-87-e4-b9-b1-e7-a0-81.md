---
title: ASP.NET MVC Ckeditor MSSQL输入韩文乱码
tags:
  - ckeditor
  - SQL SERVER
  - 乱码
id: 906
categories:
  - ASP.NET
  - MVC
  - SQL
  - WEB开发
date: 2015-10-22 19:58:40
---

具体表现为：

1中文没问题，甚至日文也没问题，单单韩文输入有问题！

2普通的input或者textare输入没问题，到Ckeditor里就有问题！

3启用调试模式查看后台接受到的没问题，但一存到数据库就有问题！

&nbsp;

解决方法：

一般因为使用ckeditor的原因，可能这个字段内容比较大，所以会将这个字段的数据库属性设置为text！只要将text类型设置成nvarchar(max)即可！

而一般为了考虑存储大小，在input里面输入的内容大小会做限制，而在mssql里面对应的字段都会设置成nvarchar。也就解释了上面表现的第二点。

网络中也建议了使用nvarchar(max)来取代text在mssql中的使用

[http://stackoverflow.com/questions/834788/using-varcharmax-vs-text-on-sql-server](http://stackoverflow.com/questions/834788/using-varcharmax-vs-text-on-sql-server "http://stackoverflow.com/questions/834788/using-varcharmax-vs-text-on-sql-server")