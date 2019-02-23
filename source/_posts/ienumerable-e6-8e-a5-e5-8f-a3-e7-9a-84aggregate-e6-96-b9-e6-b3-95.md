---
title: IEnumerable接口的Aggregate方法
id: 439
categories:
  - 'C#'
date: 2014-03-19 21:05:37
tags:
---

以前小猪为了累加一个集合中的类容通常会写出类似这样的C#代码：
``` 

 string result ="":
foreach (var item in items)
{
     result+=item.centent;
}

 ```
大概意思就是遍历集合中的每一项来累加其中的一个值。今天小猪才发现其实.NET的集合已经提供了该功能：那就是小猪现在讲的IEnumerable&lt;T&gt;接口的Aggregate方法:

该方法提供了两个重载版本

版本1：[Aggregate&lt;TSource&gt;(Func&lt;TSource, TSource, TSource&gt;)](http://msdn.microsoft.com/zh-cn/library/bb548651(v=vs.110).aspx)：已重载。 对序列应用累加器函数。 （由 [Enumerable](http://msdn.microsoft.com/zh-cn/library/system.linq.enumerable(v=vs.110).aspx) 定义。）

版本2：[Aggregate&lt;TSource, TAccumulate&gt;(TAccumulate, Func&lt;TAccumulate, TSource, TAccumulate&gt;)](http://msdn.microsoft.com/zh-cn/library/bb549218(v=vs.110).aspx)已重载。 对序列应用累加器函数。 将指定的种子值用作累加器初始值。 （由[Enumerable](http://msdn.microsoft.com/zh-cn/library/system.linq.enumerable(v=vs.110).aspx) 定义。）
``` 

 public static TAccumulate Aggregate&lt;TSource, TAccumulate&gt;(
	this IEnumerable&lt;TSource&gt; source,
	TAccumulate seed,
	Func&lt;TAccumulate, TSource, TAccumulate&gt; func
)

 ```
由定义可以看出该方法是个拓展方法，所以在使用时不需要传递第一个参数。

此方法的工作原理是对 source 中的每个元素调用一次 func。 每次调用 func 时，Aggregate&lt;TSource, TAccumulate&gt;(IEnumerable&lt;TSource&gt;, TAccumulate, Func&lt;TAccumulate, TSource, TAccumulate&gt;) 都将传递序列中的元素和聚合值（作为 func 的第一个参数）。 将 seed 参数的值用作聚合的初始值。 用 func 的结果替换以前的聚合值。Aggregate&lt;TSource, TAccumulate&gt;(IEnumerable&lt;TSource&gt;, TAccumulate, Func&lt;TAccumulate, TSource, TAccumulate&gt;) 返回 func 的最终结果。

若要简化一般的聚合运算，标准查询运算符还可以包含一个通用的计数方法（即 Count）和四个数值聚合方法（即 Min、Max、Sum 和 Average）。

下面的代码示例演示如何使用 Aggregate 应用累加器函数和使用种子值。
``` 

 int[] ints = { 4, 8, 8, 3, 9, 0, 7, 8, 2 };

// Count the even numbers in the array, using a seed value of 0.
int numEven = ints.Aggregate(0, (total, next) =&gt;next % 2 == 0 ? total + 1 : total);

Console.WriteLine("The number of even integers is: {0}", numEven);

// This code produces the following output:
//
// The number of even integers is: 6

 ```
&nbsp;

&nbsp;