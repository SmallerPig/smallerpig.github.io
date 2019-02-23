---
title: ASP.NET MVC 在子页中引用头文件
id: 318
categories:
  - ASP.NET
  - MVC
  - WEB开发
date: 2013-09-25 14:58:55
tags:
---

在很多时候我们把网站公共的js、css文件放在模板页中，这样在具体的每一个页面里面就不需要单独引用。

ASP.NET WebForm里面使用.site文件。

而在ASP.NET MVC 中使用了类似下面的定义

```
_Layout.cshtml:
<!DOCTYPE html>    
<html>    
<head>    
    <title>@ViewBag.Title</title>    
    <link href="@Url.Content("~/Content/Site.css")" rel="stylesheet" type="text/css" />    
    <script src="@Url.Content("~/Scripts/jquery-1.8.0.min.js")" type="text/javascript"></script>    
</head>    
<body>    
    @RenderBody\(\)    
</body>    
</html>
```
这样在子页中的代码就直接进入html的body中了。

但是有时候单独的页面有单独的头文件，不需要在模板页中引用。那此时需要用如下的方法了：

```
_Layout.cshtml:
<!DOCTYPE html>    
<html>    
<head>    
    <title>@ViewBag.Title</title>    
    <link href="@Url.Content("~/Content/Site.css")" rel="stylesheet" type="text/css" />    
    <script src="@Url.Content("~/Scripts/jquery-1.8.0.min.js")" type="text/javascript"></script>    
    @RenderSection("Head", required: false)    
</head>    
<body>    
    @RenderBody\(\)    
</body>    
</html>
```
注意在head节点中增加了
`@RenderSection("Head", required: false)`

这样在对应的页面中可以使用如下代码进行引用
```
@section Head{

    
    <link href="@Url.Content("~/Content/css/datebox.css")" rel="stylesheet" type="text/css" />
    
    <link href="@Url.Content("~/Content/css/easyui.css")" rel="stylesheet" type="text/css" />
    
    <script src="@Url.Content("~/Scripts/jquery.easyui.min.js")" type="text/javascript"></script>
    
    }
```
注意：如果在head节点中定义的required值为true，则必须要为每一个使用模板页的页面添加引用代码，如果required值为false，则可以增加也可以不增加。