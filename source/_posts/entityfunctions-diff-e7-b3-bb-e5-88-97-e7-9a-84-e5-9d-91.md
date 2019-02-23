---
title: EntityFunctions.Diff系列的坑
id: 724
categories:
  - 'C#'
  - SQL
date: 2015-05-30 20:33:29
tags:
---

在使用`EntityFramwork`时,当我们需要对数据库中的时间字段进行计算的时候我就需要用到`EntityFuncitons`而不能直接写成类似下面的代码了
``` 
 where(item=&gt;(item.CreateTime-DateTime.Now).Minutes&gt;2)
 ```
仔细翻阅`EntityFunctions.DiffMinutes`的文档发现其留下了这么一句话:
```
        //
        // 摘要: 
        //     调用 DiffMinutes 规范函数。有关 DiffMinutes 规范函数的信息，请参见Date and Time Canonical Functions
        //     (Entity SQL)。
        //
        // 参数: 
        //   timeValue1:
        //     有效日期。
        //
        //   timeValue2:
        //     有效日期。
        //
        // 返回结果: 
        //     timeValue1 和 timeValue2 之间的分钟数。
        [EdmFunction("Edm", "DiffMinutes")]
        public static int? DiffMinutes(DateTime? timeValue1, DateTime? timeValue2);

 ```
其上面明明写着返回结果是timeValue1和timeValue2之间的分钟数.类似的方法还有`DiffMonths\(\)`,`DiffSeconds\(\)`,`DiffYears\(\)`..

当我们信誓旦旦的写上代码:
``` 
 Where(item =&gt; item.Status == 0 &amp;&amp; EntityFunctions.DiffMinutes(DateTime.Now, item.CreateTime)&gt;= 15)

 ```
却怎么也得不到想要的结果..

通过sql监视发现其生成的SQL语句是
``` 
 DATEDIFF (minute, SysDateTime\(\), SysDateTime\(\))&gt;=15

 ```
单独执行才发现,这两个参数的顺序反了.简直是我勒个擦了
``` 
 Where(item =&gt;item.Status == 0 &amp;&amp; EntityFunctions.DiffMinutes(item.CreateTime, DateTime.Now)&gt;= 15)

 ```
也就是说:EntityFunctions.Diff系列函数的调用,第一个参数得比第二个参数小,才能返回正数结果.