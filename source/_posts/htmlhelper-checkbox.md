---
title: Htmlhelper—CheckBox自动生成两个input
id: 355
categories:
  - ASP.NET
  - 'C#'
  - HTML
  - MVC
  - WEB开发
date: 2013-12-02 11:41:53
tags:
---

## 前言

在之前的一篇文章中小猪分享了[Htmlhelper](http://www.smallerpig.com/archives/353)的用法。其中有意思的一个就是Checkbox，有必要单独拿出来讲一讲。

## Htmlhelper—CheckBox

细心的读者一定发现了当使用类似 语法
```
@Html.CheckBox("recommend")
 ```
&nbsp;

生成的html中除了一个 type="checkbox"的表单元素之外另外还生成了一个 type="hidden"的隐藏元素
``` 
 <input id="recommend" name="recommend" type="checkbox" value="true">
<input name="recommend" type="hidden" value="false">
 ```
&nbsp;

这两个表单元素都有一个 name为"recommend"的属性。type为checkbox的表单元素value为true，type为hidden的表单元素value值为false。

当我们不选中该CheckBox值时通过监听网络请求可以发现请求值
``` 
 recommend=false
 ```
&nbsp;

当选中 CheckBox值时发现请求值为
``` 
 recommend=true&amp;recommend=false
 ```

### 看看一个老外的解释：

The HtmlHelper.CheckBox method puts in a hidden value with the same name at the checkbox. The reason this is done is because if the checkbox is not checked, the browswer doesn't send a value for the checkbox with the form post. The way around this is via the hidden variable which results in a value of false if the checkbox is not checked or true,false if it is checked. 

It's not confused, it's just reporting the values it has.When you use the CHeckBox helper, the framework spits out two input controls, one for the checkbox itself, and a hidden one with the same name that's value is false. it does this to help the default model binder always have a value for the name in question, because HTML forms don't append cleared checkboxes to the data. So you have two choices. You can either use the method you're currently using, but use string parsing to extract the "first" value (which equates to the boolean value you're looking for) or else you can instead use a custom model object as the input param of the POST action, to let the model binder do it's job. [http://forums.asp.net/t/1440058.aspx](http://forums.asp.net/t/1440058.aspx)

通过上面俩个老外的解释我们可以看出：在表单提交时，如果 Checkbox不选中的话那么是不会提交该Checkbox的值的，这个时候在后台也就没有办法得到该字段，而MVC默认的给该字段加上了一个隐藏的false值，当前台没选中的话就把false传过去，而选中了的话则把true(Checkbox的值)和false(隐藏域的值)都以相同的name穿过去让后台来处理，再通过ASP.NET MVC自带的模型绑定功能就会自动的将这个bool值转换成模型中的值，很大的方便了我们编程。

&nbsp;

如果我们自己手写html代码
``` 
 <input id="recommend" name="recommend" type="checkbox" checked="checked">
 ```
则不能够直接在Controller中使用类似
``` 
 public ActionResult Create(bool Recommend)
{
    // do something
    return RedirectToAction("List");
}
 ```
&nbsp;

这样的代码，因为本身的html input的Checkbox在选中时只传递一个on值(如果没有value属性时)，得像下面这样获取才能实现一个很简单的效果，
``` 
 public ActionResult Create(TB_Book tb_book ,string Recommend)
{
    if (Recommend == "on")
    {
       //do something
    }                
   //do something
    return RedirectToAction("List");
}
 ```
&nbsp;

这样就得把是bool值的字段都单独拿出来写。好麻烦的说！