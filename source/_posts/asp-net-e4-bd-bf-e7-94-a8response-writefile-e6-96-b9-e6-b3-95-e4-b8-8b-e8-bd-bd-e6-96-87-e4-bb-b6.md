---
title: ASP.NET 使用Response.WriteFile方法下载文件
id: 257
categories:
  - ASP.NET
  - 'C#'
  - WEB开发
date: 2013-08-12 15:59:41
tags:
---

在IIS中，直接在浏览器中输入文件的路径可实现文件的下载，但是这个方法不方便控制用户的权限，所以小猪使用了下列方法来输出文件流。取代了直接下载文件。

这样就可以在下载文件之前验证用户的信息等等。

```
string path = Server.MapPath("~/小猪测试.doc");//文件的路径

System.IO.FileInfo file = new System.IO.FileInfo(path);

Response.Clear\(\);

Response.Charset = "utf-8";//设置输出的编码

Response.ContentEncoding = System.Text.Encoding.UTF8;

// 添加头信息，为"文件下载/另存为"对话框指定默认文件名   

Response.AddHeader("Content-Disposition", "attachment; filename=" + Server.UrlEncode(file.Name));

// 添加头信息，指定文件大小，让浏览器能够显示下载进度   

Response.AddHeader("Content-Length", file.Length.ToString\(\));

// 指定返回的是一个不能被客户端读取的流，必须被下载   

Response.ContentType = "application/msword";

// 把文件流发送到客户端   

Response.WriteFile(file.FullName);

Response.End\(\);
```
重要说明：当您在 ASP.NET 应用程序的 Web.config 文件中将编译元素的 debug 属性值设置为 false 时，必须针对要下载的文件大小将 server.scripttimeout 属性设置为适当的值。默认情况下，server.scripttimeout 值被设置为 90 秒。但是，当 debug 属性被设置为 true 时，server.scripttimeout 值将被设置为一个非常大的值（30,000,000 秒）

本篇向大家介绍的主要是WebForm下面的文件下载，在ASP.NET MVC 项目下则可以更加方便的方法使用相同的功能。

具体请参考：[ASP.NET MVC FileResult介绍](http://www.smallerpig.com/archives/272)