---
title: event.srcElement在火狐(FireFox)下的兼容问题。搜索框获得焦点时默认文字变化
id: 344
categories:
  - HTML
  - JS
  - WEB开发
date: 2013-11-13 08:44:44
tags:
---

```
## 

前言：

	项目中用到了一个功能，搜索框里有默认的文字，当搜索框获得焦点时里面的默认文字消失，如果失去焦点时搜索框内容为空则让里面的内容回复默认!,.

```
## 

实现：

	很轻松的在网上找到了类似代码

``` js
    $("#search_text").focus(function (event) {
        with (event.srcElement)
            //如果当前值为默认值，则清空 
            if (value == defaultValue) value = "";
    });
    $("#search_text").blur(function res(event) {
        //捕获触发事件的对象，并设置为以下语句的默认对象 
        with (event.srcElement)
            //如果当前值为空，则重置为默认值 
            if (value == "") {
                value = defaultValue;
            }
    });

 ```

	该代码很牛逼，用到了js里面的with语法(虽然书上说因为性能原因不建议使用with)，而且完全支持IE6。

	但是问题来了，连IE都支持的这段代码竟然在火狐上面出现了兼容的问题。

	查了资料原来是

	event.srcElement 意思是当前事件的源，我们可以调用它的各种属性，就像 document.getElementById(id) 一样。

	IE下，event对象有 srcElement 属性，没有target属性；firefox下，event对象有 target属性，但是没有 srcElement 属性。他们的作用是一样的。

## 版本二：

``` js
    $("#search_text").focus(function (event) {
        with (event.target || event.srcElement)
            //如果当前值为默认值，则清空 
            if (value == defaultValue) value = "";
    });
    $("#search_text").blur(function res(event) {
        //捕获触发事件的对象，并设置为以下语句的默认对象 
        with (event.target || event.srcElement)
            //如果当前值为空，则重置为默认值 
            if (value == "") {
                value = defaultValue;
            }
    });

 ```
	好了，可以让你的代码在项目中运行起来了