---
title: 'C#操作JSON字符串'
id: 292
categories:
  - 'C#'
  - JS
  - WEB开发
date: 2013-08-27 10:18:26
tags:
---

在前面的文章中，小猪分享过如何将json字符串转换成js对象，具体请看[传送门](http://www.smallerpig.com/archives/283 "传送门")。

那如果是前台通过js等其他东东发送过来的json字符串我们要如何将其转换成C#对象呢？

如果是post过来的json数组的话我们可以直接使用Request.Form[&quot;&quot;]的方式获取值。

今天小猪分享的是如何将json数组转换成C#对象。

**首先引用，**
```


using System.Web.Script.Serialization;

 ```

**第二部：定义实体类**
```


class Entity
{
    public int status { get; set; }
}

 ```

**第三部：定义泛型转换**
```


public static T JSONToObject&lt;T&gt;(string jsonText)
{
    JavaScriptSerializer jss = new JavaScriptSerializer\(\);
    return jss.Deserialize&lt;T&gt;(jsonText);
}

 ```

**第四部：使用**
```


Entity entity= JSONToObject&lt;Entity&gt;(JsonString);

 ```

**特别注意：**

使用的json字符串中的数据字段必须大于等于实体类中的属性。也就是实体类中有的属性则json字符串中也有，否则可能保存，相反，json字符串中有的则实体类中不一定要有。