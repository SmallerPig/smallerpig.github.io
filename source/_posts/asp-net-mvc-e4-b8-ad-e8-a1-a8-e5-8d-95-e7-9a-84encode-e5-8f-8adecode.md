---
title: ASP.NET MVC 中表单的Encode及Decode
tags:
  - ASP.NET
  - 'C#'
  - HTML
  - MVC
id: 203
categories:
  - MVC
date: 2013-06-27 14:30:42
---

前端cshtml里面需要decode时使用
`@Html.Raw(HttpUtility.HtmlDecode(Model.Contents))`

对应的action里需要encode的地方使用
`HttpUtility.HtmlEncode(collection["newsContentData"]);`