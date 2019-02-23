---
title: jQuery ajax在IE下处理url=时的一个不兼容
tags:
  - HTML
  - JS
id: 225
categories:
  - JS
  - WEB开发
date: 2013-07-18 09:33:30
---

小猪之前写了这么个代码：
```
AJAX = function (data, url, beforesendfn, onsuccessfn, onerrorfn, oncomplete) {
       $.ajax({
            type: "POST",
            url:url
            cache: false,
            data: JSON.stringify(data),
            contentType: "application/json; charset=utf-8",
            dataType: "json",
            beforeSend: beforesendfn,
            success: onsuccessfn,
            error: onerrorfn,
            complete: oncomplete
        });
}
```

这段代码实际上是没有什么问题的。

但是在调用代码的时候小猪使用了的参数
```
var action = url == undefined ? "" : url;
```

这样在没有定义url的情况下url转换成“”。本意是想Post到当前页面地址。

在Chrome下和FireFox下都是没问题的，但是在IE下却不能post到对应地址。

所以为了兼容IE 小猪只好写了下述可怜的代码
```
AJAX = function (data, url, beforesendfn, onsuccessfn, onerrorfn, oncomplete) {
    if (url == undefined || url == "") {
        $.ajax({
            type: "POST",
            cache: false,
            data: JSON.stringify(data),
            contentType: "application/json; charset=utf-8",
            dataType: "json",
            beforeSend: beforesendfn,
            success: onsuccessfn,
            error: onerrorfn,
            complete: oncomplete
        });
    } else {
        $.ajax({
            type: "POST",
            url:url,
            cache: false,
            data: JSON.stringify(data),
            contentType: "application/json; charset=utf-8",
            dataType: "json",
            beforeSend: beforesendfn,
            success: onsuccessfn,
            error: onerrorfn,
            complete: oncomplete
        });
    }
}
```