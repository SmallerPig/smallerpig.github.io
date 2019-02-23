---
title: '[ASP.NET MVC]在return View\(\)之前向输出流写入'
id: 737
categories:
  - ASP.NET
  - 'C#'
  - MVC
date: 2015-06-02 16:27:11
tags:
---

直接上代码:

控制器部分代码:
```
public ActionResult Test\(\)

    
{




Response.Write("hello world");

return View("Index");




    
}
```
视图部分代码
```
<html xmlns="http://www.w3.org/1999/xhtml">

    
<head>
    
    <title></title>
    
</head>
    
<body>
    
    <h2>index of pc</h2>
    
</body>
    
</html>
```
在调用上述代码之后看返回给浏览器的最终代码:

[![image](http://www.smallerpig.com/wp-content/uploads/2015/06/image_thumb.png "image")](http://www.smallerpig.com/wp-content/uploads/2015/06/image.png)

可见,hello world输出流在整个view之前呈现了.

由此我们可以推断加证明,`return View\(\)`方式只是将cshtml文件以字符的形式输出出来.,当然`ViewResult\(\)`本身加载的视图引擎要复杂的多.!

那么如果我们在return View\(\)之前把Respon给End\(\)之后会怎么样呢?再来试验一下!

controller代码:
```
public ActionResult Test\(\)

    
{
    
    Response.Write("hello world");
    
    Response.End\(\);




return View("Index");

}
```
View的代码不变,我们运行程序看看生成的代码:
`hello world`
可见,在Response.End\(\)之后,再向输出流输出内容就输出不了了.

参考http://www.cnblogs.com/artech/archive/2012/08/15/action-result-03.html