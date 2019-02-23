---
title: 在ASP.NET MVC 中获取当前URL、controller、action
id: 345
categories:
  - ASP.NET
  - 'C#'
  - MVC
  - WEB开发
date: 2013-11-13 16:36:48
tags:
---

```
## 

URL的获取很简单，ASP.NET通用：&nbsp;

#```
## 

【1】获取&nbsp;完整url&nbsp;（协议名+域名+虚拟目录名+文件名+参数）&nbsp;

	string&nbsp;url=Request.Url.ToString\(\);

	&nbsp;

#```
## 

【2】获取&nbsp;虚拟目录名+页面名+参数：&nbsp;

	string&nbsp;url=Request.RawUrl;

	(或&nbsp;string&nbsp;url=Request.Url.PathAndQuery;)

	&nbsp;

#```
## 

【3】获取&nbsp;虚拟目录名+页面名：

	string&nbsp;url=HttpContext.Current.Request.Url.AbsolutePath;

	(或&nbsp;string&nbsp;url=&nbsp;HttpContext.Current.Request.Path;)

	&nbsp;

#```
## 

【4】获取&nbsp;域名：

	string&nbsp;url=HttpContext.Current.Request.Url.Host;

	&nbsp;

#```
## 

【5】获取&nbsp;参数：&nbsp;

	string&nbsp;url=&nbsp;HttpContext.Current.Request.Url.Query;&nbsp;

	&nbsp;

#```
## 

【6】获取&nbsp;端口：

	Request.Url.Port

	&nbsp;

```
## 

二、当前controller、action的获取&nbsp;

	RouteData.Route.GetRouteData(this.HttpContext).Values["controller"]&nbsp;

	RouteData.Route.GetRouteData(this.HttpContext).Values["action"]&nbsp;

	或&nbsp;

	RouteData.Values["controller"]&nbsp;

	RouteData.Values["action"]&nbsp;

	&nbsp;

	如果在视图中可以用&nbsp;

	ViewContext.RouteData.Route.GetRouteData(this.Context).Values["controller"]&nbsp;

	ViewContext.RouteData.Route.GetRouteData(this.Context).Values["action"]&nbsp;

	或&nbsp;

	ViewContext.RouteData.Values["controller"]&nbsp;

	ViewContext.RouteData.Values["action"]