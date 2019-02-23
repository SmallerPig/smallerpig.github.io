---
title: 在ASP.NET MVC中使用Area
id: 378
categories:
  - ASP.NET
  - MVC
  - WEB开发
date: 2013-12-30 14:47:44
tags:
---

```
## 

前言：

	这段时间小猪花了不少功夫在研究ASP.NET MVC的源码上面，可谓思想是了解了不少，用的上用不上却是另外一回事了。！

```
## 

应用场景：

	ASP.NET MVC中,是依靠某些文件夹以及类的固定命名规则去组织model实体层，views视图层和控制层的。如果是大规模的应用程序，经常会由不同功能的模块组成，而每个功能模块都由MVC中的三层所构成，因此，随着应用程序规模的增大，如何组织这些不同功能模块中的MVC三层的目录结构，有时对开发者来说显得是种负担。

	另一个问题就是Controller不允許有相同命名的存在，偏偏模块中常有父子关系，有时子模块命名相同就会造成错误。

	幸运的是，ASP.NET MVC允许开发者将应用划分为&ldquo;区域&rdquo;(Area)的概念。

```
## 

Area(区域)简介：

	每个区域都是按照asp.net mvc的规定对文件目录结构和类的命名规则进行命名。添加方法也很简单，右击项目，点击添加区域，在弹出框里写上Area的名称点击确定即可。

	&nbsp;*&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;1、新建&nbsp;Area：右键&nbsp;-&gt;&nbsp;Add&nbsp;-&gt;&nbsp;Area...

	&nbsp;*&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;2、继承&nbsp;AreaRegistration，配置对应此&nbsp;Area&nbsp;的路由

	&nbsp;*&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;3、在&nbsp;Global&nbsp;中通过&nbsp;AreaRegistration.RegisterAllAreas\(\);&nbsp;注册此&nbsp;Area

	&nbsp;*&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4、有了&nbsp;Area，就一定要配置路由的命名空间

```
## 

Area的路由

	在添加好了区域之后，vs会自动帮我们注册Area的路由信息：

``` 

 
public class AdminAreaRegistration : AreaRegistration
{
    public override string AreaName
    {
        get
        {
            return &quot;Admin&quot;;
        }
    }

    public override void RegisterArea(AreaRegistrationContext context)
    {
        context.MapRoute(
            &quot;Admin_default&quot;,
            &quot;Admin/{controller}/{action}/{id}&quot;,
            new { controller = &quot;Console&quot;, action = &quot;Login&quot;, id = UrlParameter.Optional }
        );
    }
}

 ```

#```
## 

问题

	试想如果这时候有这样的情形：

	如果有一个Area叫Database，在它的下面有一个Controller名字叫做Browse。

	另外我在顶层也有一个Controller，名字也叫Browse。

	那么我们在<span style="font-size: 13px; line-height: 1.6em;">输入http://localhost/Browse，我们得到的将是一个异常:&nbsp;Multiple types were found that match the controller named &#39;Browse&#39;.&nbsp;</span>

	而输入http://localhost/Database/Browse，我们却能够正常地访问到 MvcApplication.Areas.Database.Controllers.BrowseController。

#```
## 

原来：

	在注册Area的路由时，如果没有填写命名空间的话，则会默认使用Area所在的命名空间。如此一来，使用Area的路由在寻找Controller时，只会在Area所在的空间下寻找相应的Controller，那就不存在与顶层Controller的冲突问题了，可是顶层的同名Controller问题如何解决呢，这个好办，在顶层路由映射的时候主动加上命名空间吧，这样子就皆大欢喜，你用你的Browse，我用我的Browse，互不相干。

``` 

 
public static void RegisterRoutes(RouteCollection routes)
{
     routes.IgnoreRoute(&quot;{resource}.axd/{*pathInfo}&quot;);

     routes.MapRoute(
        &quot;Default&quot;, // Route name
        &quot;{controller}/{action}/{id}&quot;, // URL with parameters
        new { controller = &quot;Home&quot;, action = &quot;Index&quot;, id = UrlParameter.Optional },// Parameter defaults
        new[] { &quot;Web.Controllers&quot; }// Namespaces 引入默认的命名空间
        );

}

 ```

```
## 

让起始页为某Area的页面：

	修改RoutConfig代码：

``` 

 
public static void RegisterRoutes(RouteCollection routes)
{
    routes.IgnoreRoute(&quot;{resource}.axd/{*pathInfo}&quot;);

    routes.MapRoute(
        name: &quot;Default&quot;,
        url: &quot;{controller}/{action}/{id}&quot;,
        defaults: new { controller = &quot;Console&quot;, action = &quot;Login&quot;, id = UrlParameter.Optional }
    ).DataTokens.Add(&quot;area&quot;,&quot;Admin&quot;);
}

 ```

	注意后面的DataTokens.Add方法！

	Done！