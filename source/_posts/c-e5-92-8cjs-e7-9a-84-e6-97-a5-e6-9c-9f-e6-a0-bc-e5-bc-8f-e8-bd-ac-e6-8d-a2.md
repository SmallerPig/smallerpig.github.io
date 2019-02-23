---
title: 'C#和JS的日期格式转换'
id: 286
categories:
  - ASP.NET
  - 'C#'
  - JS
  - WEB开发
date: 2013-08-19 14:04:32
tags:
---

C#和JS的日期格式互相转换遇到了问题，我们从.NET服务器端序列化一个DateTime对象的结果是一个字符串格式，如 <span style="color:#FF0000;">&#39;/Date(1335258540000)/&#39;</span> 这样的字符串。

整数1335258540000实际上是一个<span style="color:#FF0000;">1970 年1月1日00:00:00</span>至这个DateTime中间间隔的毫秒数。通过javascript用eval函数可以把这个日期字符串转换为一个带有时区的Date对象，如下

用
```
var date = eval('new ' + eval('/Date(1335258540000)/').source)
```

这样即可得到一个JS对象

通过alert(date)查看比较清楚。

<span style="color:#FF0000;">Tue Apr 24 17:09:00 UTC+0800 2012 </span>

然后通过js的时间函数可对其进行格式化。

```
var myDate = new Date\(\);
myDate.getYear\(\);        //获取当前年份(2位)
myDate.getFullYear\(\);    //获取完整的年份(4位,1970-????)
myDate.getMonth\(\);       //获取当前月份(0-11,0代表1月)
myDate.getDate\(\);        //获取当前日(1-31)
myDate.getDay\(\);         //获取当前星期X(0-6,0代表星期天)
myDate.getTime\(\);        //获取当前时间(从1970.1.1开始的毫秒数)
myDate.getHours\(\);       //获取当前小时数(0-23)
myDate.getMinutes\(\);     //获取当前分钟数(0-59)
myDate.getSeconds\(\);     //获取当前秒数(0-59)
myDate.getMilliseconds\(\);    //获取当前毫秒数(0-999)
myDate.toLocaleDateString\(\);     //获取当前日期
var mytime=myDate.toLocaleTimeString\(\);     //获取当前时间 上午12:00:00
myDate.toLocaleString&#40; &#41;;        //获取日期与时间 2012年12月12日 上午12:00:00
```