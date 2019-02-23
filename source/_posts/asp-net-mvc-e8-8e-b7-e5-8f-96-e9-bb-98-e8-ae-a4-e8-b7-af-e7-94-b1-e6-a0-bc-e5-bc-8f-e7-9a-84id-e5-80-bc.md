---
title: '[ASP.NET MVC]获取默认路由格式的id值'
tags:
  - ASP.NET MVC
  - RouteData
id: 778
categories:
  - ASP.NET
  - MVC
  - WEB开发
date: 2015-06-19 10:48:41
---

我们都知道,在默认的ASP.NET MVC程序中,其自动为我们加入了路由格式/Controller/Action/Id.其中id为可选值,所以我们在浏览器中输入/home/index时才会给我们调用`HomeController`下的`Index`方法.如果输入/home/index/3,则程序会自动把3作为Index方法的id参数给传递进去.

这个时候虽然是使用了类似get的方法来传递参数,我们却不能使用`Request.QueryString`的方法来获取id值了,来看代码:

控制器代码:
```
  public ActionResult Index(int id = 0)
  {
      return View\(\);
  }
```
View代码:
```
<div>QueryString: @Request.QueryString["id"]</div>
```

看下运行效果:如果输入http://localhost:29400/Home/Index/3.输出结果为:
```
<div>QueryString: </div>
```
输入http://localhost:29400/Home/Index?id=3.输出结果为:
```
<div>QueryString: 3</div>
```

可见,并不能使用这样的方法了.

那怎么在view中获取url为http://localhost:29400/Home/Index/3中3这个值呢?可是使用上两篇博客中提到的[RouteData](http://www.smallerpig.com/763.html)类了.

我们可以在View中这样来取值:
```
<div>RouteData: @ViewContext.RouteData.Values["id"]</div>
```
这样我们就能够得到正确的结果!但是另外一个问题是使用这样的方法获取id值却不能得到url为:http://localhost:29400/Home/Index?id=3中id的值.

所以在项目中到底使用哪种方式来取值还是得看具体的需求来定了.