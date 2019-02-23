---
title: ASP.NET MVC FileResult介绍
id: 272
categories:
  - ASP.NET
  - 'C#'
  - MVC
  - WEB开发
date: 2013-08-13 10:10:09
tags:
---

在前一篇中，小猪向大家分享了在WebForm中[使用Response.WriteFile\(\)方法来向客户端提供指定文件的下载](http://www.smallerpig.com/archives/257)。这篇小猪向大家介绍在ASP.NET MVC中怎么为客户端实现相同的功能。

我们可以定义Control的返回类型为FileResult来向客户端提供文件的下载。
```Public FileResult DownLoad\(\)
{
    //Some code
    ...
}
```

FileResult 是一个抽象类，继承自 ActionResult。在 System.Web.Mvc.dll 中，它有如上三个子类，分别以不同的方式向客户端发送文件。

在实际使用中我们通常不需要直接实例化一个 FileResult 的子类，因为 Controller 类已经提供了六个 File 方法来简化我们的操作：
```protected internal FilePathResult File(string fileName, string contentType);
protected internal virtual FilePathResult File(string fileName, string contentType, string fileDownloadName);
protected internal FileContentResult File(byte[] fileContents, string contentType);
protected internal virtual FileContentResult File(byte[] fileContents, string contentType, string fileDownloadName);
protected internal FileStreamResult File(Stream fileStream, string contentType);
protected internal virtual FileStreamResult File(Stream fileStream, string contentType, string fileDownloadName);
```

**1.最简单的方法**
```public ActionResult FilePathDownload1\(\)
{
    var path = Server.MapPath("~/Files/小小猪.zip");
    return File(path, "application/x-zip-compressed");
}
```

第一个参数指定文件路径，第二个参数指定文件的 MIME 类型。

用户点击浏览器上的下载链接后，会调出下载窗口。

大家应该注意到，文件名称会变成 Download1.zip，默认成了 Action 的名字。我们使用 File 方法的第二个重载来解决文件名的问题：

**2.为文件指定默认下载名**
```public ActionResult FilePathDownload2\(\)
{
    var path = Server.MapPath("~/Files/小小猪.zip"); 
    return File("F:\小小猪.zip", "application/x-zip-compressed", "crane.zip");
}

public ActionResult FilePathDownload3\(\)
{
    var path = Server.MapPath("~/Files/小小猪.zip"); 
    var name = Path.GetFileName(path);
    return File(path, "application/x-zip-compressed", name);
}
```

我们可以通过给 fileDownloadName 参数传值来指定文件名，fileDownloadName 不必和磁盘上的文件名一样。下载提示窗口还是默认为了 Action 的名字。原因是 fileDownloadName 将作为 URL 的一部分，只能包含 ASCII 码。我们把 FilePathDownload3 改进一下：

**3\. 对 fileDownloadName 进行 Url 编码**
```public ActionResult FilePathDownload4\(\)
{
    var path = Server.MapPath("~/Files/小小猪.zip");
    var name = Path.GetFileName(path);
    return File(path, "application/x-zip-compressed", Url.Encode(name));
}
```

好了，没问题了。上面代码中 Url.Encode(…)，也可使用 HttpUtility.UrlEncode(…)，前者在内部调用后者。

我们再来看 FileContentResult.

**4.FileContentResult**

FileContentResult 可以直接将 byte[] 以文件形式发送至浏览器（而不用创建临时文件）。参考代码如下：
```public ActionResult FileContentDownload1\(\)
{
    byte[] data = Encoding.UTF8.GetBytes("欢迎访问 小小猪 的博客 www.smallerpig.com");
    return File(data, "text/plain", "welcome.txt");
}
```

点击后下载链接后，弹出提示窗口是否下载“welcome.txt”