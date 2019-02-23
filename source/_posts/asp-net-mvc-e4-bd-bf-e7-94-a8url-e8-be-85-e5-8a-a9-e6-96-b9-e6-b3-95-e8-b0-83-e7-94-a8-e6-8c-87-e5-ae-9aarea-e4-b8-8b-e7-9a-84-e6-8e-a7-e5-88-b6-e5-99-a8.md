---
title: ASP.NET MVC使用Url辅助方法调用指定Area下的控制器
tags:
  - 路由
id: 908
categories:
  - ASP.NET
  - MVC
  - WEB开发
date: 2015-10-26 22:18:35
---

我们都知道在生成url链接的时候，官方是推荐使用Url.Action\(\)方法来生成的。框架会自动帮我们生成指定的url而且一般来说不会出错。小猪只从知道有这么个方法之后一直对它相见恨晚，特别是其能够通过优雅的代码生成指定的get参数。

可是今天小猪却在这个方法上面栽了个跟头……

原因是这样的：因为要在某个Area的控制器下生成不在该Area下的url。例如在admin的area下生成默认默认命名空间下的HomeController中的Index方法，小猪自然的想到使用代码Url.Action(“Index”,”Home”)来生成。可是生成的却是：/Admin/Home/Index。。为此小猪在area下面的路由注册了下面的方法：
```
context.MapRoute(
    "home",
    "Home/{action}",
    new { controller = "Home", action = "Login" },
    namespaces: new[] { "ConsoleProject.Controllers" }
    );
```

这样貌似在该area下面生成Home控制器的url是没问题的。小猪也以为这个需求就这么结束了……

可是结果却是在原来的默认不在该area的url.action\(\)方法都会生成该area下的url。这下蛋疼了。例如使用代码

@Url.Action("Index", "Console", new { articleId = 343 })生成的链接竟然是：/Admin/Console/Index?articleId=343

谷歌了好一会才知道Url.Action\(\)方法会自动使用第一个能够匹配的路由来生成URL。很显然area的路由注册是在本身的default路由之前的。这个可通过Global.asax的部分代码看出来：
```
protected void Application_Start\(\)
{
    AreaRegistration.RegisterAllAreas\(\);
    WebApiConfig.Register(GlobalConfiguration.Configuration);
    FilterConfig.RegisterGlobalFilters(GlobalFilters.Filters);
    RouteConfig.RegisterRoutes(RouteTable.Routes);
    ControllerBuilder.Current.SetControllerFactory(new DefaultControllerFactory\(\));
}
```
而在新建area的时候会自动注册路由Admin_default：
```
context.MapRoute(
    "Admin_default",
    "Admin/{controller}/{action}/{id}",
    new { controller = "Console", action = "Login", id = UrlParameter.Optional }
);
```

这个时候使用@Url.Action("Index", "Console", new { articleId = 343 })生成的链接就是：/Admin/Console/Index?articleId=343。因为这个时候首先匹配的就是名称为Admin_default的路由。

不过有一个问题小猪搞不明白的是为啥只有在注册了home路由的时候才会这个问题。而默认仅有Admin_default的时候就没有这个问题了！

所以一开始使用注册新路由的方法只会使原来的项目更糟糕，需要修改大量原来使用Url.Action\(\)方法的地方。

最终取消了所有自己注册的路由，只使用系统默认的两个路由，而在需要制定使用某个路由生成URL的时候使用Url.RouteUrl\(\)方法：@Url.RouteUrl("default",new {controller = "Library",action="AnnouncementContent",articleId=item.Id})
这样就只在需要“跨域”生成链接的时候稍微麻烦点，而不必要动整个项目了！