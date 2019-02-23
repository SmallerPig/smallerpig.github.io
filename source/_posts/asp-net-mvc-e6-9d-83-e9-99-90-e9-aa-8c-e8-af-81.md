---
title: ASP.NET MVC权限验证
tags:
  - ASP.NET
  - 'C#'
  - MVC
  - 权限验证
id: 204
categories:
  - ASP.NET
  - MVC
  - WEB开发
date: 2013-06-27 17:21:42
---

在WEB FORM 里可以写一个通用类继承自Page类，在其Page_Load\(\)事件中对session进行验证。

而在MVC中可以使用C#的&quot;特性&quot;(或者叫过滤器)来进行身份验证。

自定义一个过滤器，重写其OnActionExecuting(ActionExecutingContext)方法。该方法是在Action执行之前先进行验证。

```
public class LoginFilter : System.Web.Mvc.ActionFilterAttribute
{
    public override void OnActionExecuting(System.Web.Mvc.ActionExecutingContext
       filterContext)
    {
        if (filterContext.HttpContext.Session["admin"] == null)
        {
            filterContext.HttpContext.Response.Redirect("/Console/Login");
        }
    }
}
```

然后在需要登录的Action上面加上该过滤器即可。
```
[LoginFilter]
public ActionResult Main\(\)
{
    //do somthing
    return View\(\);
}
```

**<span style="font-size:18px;">Controller的Filter</span>**：
Monorail中的Filter是可以使用在Controller中的,这给编程者带来了很多方便,那么在Asp.net MVC中可不可以使用Controller级的Filter呢.不言自喻.

实现方法如下Controller本身也有OnActionExecuting与OnActionExecuted方法 ,将之重写即可,见代码
```
sing System.Web.Mvc;
    public class EiceController : Controller
    {
        public void Index\(\) {
            this.HttpContext.Session["temp"] += "Action";

            RenderView("Index");
        }
        public void Index2\(\) {

            RenderView("Index");
        }
        protected override void OnActionExecuting(FilterExecutingContext
           filterContext) {
            filterContext.HttpContext.Session["temp"] += "OnActionExecuting;";
        }

        protected override void OnActionExecuted(FilterExecutedContext
            filterContext) {
            filterContext.HttpContext.Session["temp"] += "OnActionExecuted";
        }
    }
```

注意两个方法是protected 的签名。