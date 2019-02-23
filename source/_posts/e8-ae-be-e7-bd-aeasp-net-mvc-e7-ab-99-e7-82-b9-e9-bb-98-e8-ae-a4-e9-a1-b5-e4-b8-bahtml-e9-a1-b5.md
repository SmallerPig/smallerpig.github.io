---
title: 设置ASP.NET MVC站点默认页为html页
id: 346
categories:
  - ASP.NET
  - 'C#'
  - HTML
  - MVC
  - WEB开发
date: 2013-11-15 11:15:48
tags:
---

# 
	问题由来

	部署了一个Asp.Net MVC的站点,其功能只是作为移动端的服务器，服务器空间里面除了CMS以外就没有其他的页面了。这对于我们来说确实是有点浪费了。

	可以放点静态的啥小东西放在上面玩一玩。

	&nbsp;

	所以就出现了标题中出现的问题。

# 
	解决方案：

#```
## 

方法1：

	在Global.asax文件中增加

	protected void Application_BeginRequest(object sender, EventArgs e)

	{

	&nbsp;&nbsp;&nbsp;&nbsp;if (Context.Request.FilePath == &quot;/&quot;) Context.RewritePath(&quot;index.html&quot;);

	}

#```
## 

方法2：

	新建一个路由DefaultController，并把它设置为默认路由，在Action中增加

	Redirect(Url.Content(&quot;~/index.html&quot;));

	此方法需要修改web.config配置

	在Web.config文件中的&lt;compilation&gt;节点中增加：

	&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&lt;buildProviders&gt;

	&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&lt;add extension=&quot;.htm&quot; type=&quot;System.Web.Compilation.PageBuildProvider&quot; /&gt;

	&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&lt;/buildProviders&gt;

	&nbsp;&nbsp;

#```
## 

方法3：

	1)站点根目录增加了default.html;

	2)修改Global.asax默认的路由注册，去掉默认controller：

	routes.MapRoute(

	&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&quot;Default&quot;, // 路由名称

	&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&quot;{controller}/{action}/{id}&quot;, // 带有参数的 URL

	&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;new {action = &quot;Index&quot;, id = UrlParameter.Optional } // 参数默认值

	&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;);&nbsp;

	&nbsp;

	将iis中的默认文档配置为index.html