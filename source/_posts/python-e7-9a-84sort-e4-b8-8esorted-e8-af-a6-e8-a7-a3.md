---
title: python的sort\(\)与sorted\(\)详解
tags:
  - python
id: 1154
comment: false
categories:
  - python
date: 2017-02-22 15:52:02
---

两种方法默认都使用ASCII值使用冒泡算法来从小到大进行排序。

## 何为冒泡算法？

摘自百度百科：

> 冒泡排序（Bubble Sort），是一种计算机科学领域的较简单的排序算法。
>   它重复地走访过要排序的数列，一次比较两个元素，如果他们的顺序错误就把他们交换过来。走访数列的工作是重复地进行直到没有再需要交换，也就是说该数列已经排序完成。
>   这个算法的名字由来是因为越大的元素会经由交换慢慢“浮”到数列的顶端，故名。

sort是List的一个方法、sorted方法是python的内置函数（built-in）。

sort将原列表重新排序、sorted方法返回新的经过排序的列表，原列表保持不变。

sort示例
```
>>> l=[2,1,3]
>>> l.sort\(\)
>>> l
[1, 2, 3]


```

sorted示例

```  

>>> l=[2,1,3]
>>> l
[2, 1, 3]
>>> s = sorted(l)
>>> s
[1, 2, 3]
>>> l
[2, 1, 3]


```

## sorted方法高级用法。

官方文档中对sorted的描述如下：

> sorted(iterable[, cmp[, key[, reverse]]])
> Return a new sorted list from the items in iterable.

可以看到该函数可以通过传递另外参数来自定义排序规则。

### cmp

该参数需要传入一个比较函数，该函数需要传入两个参数(x1,x2)来进行比较，如果自定义的规则是x1>x2,那么则返回正数，如果规则是x1&lt;x2则返回负数，规则是x1=x2的话则返回0。

```  

def cmp(x, y):
if x > y:
    return -1
if x &lt; y:
    return 1
return 0

sorted([36, 5, 12, 9, 21], cmp)



```

上述例子中返回结果为：[36, 21, 12, 9, 5]

使用匿名函数的例子：

```  

>>> l = [('b',2),('a',1),('c',3),('d',4)]
>>> sorted(l, cmp=lambda x,y:cmp(x[1],y[1]))
[('a', 1), ('b', 2), ('c', 3), ('d', 4)]


```

### key

可以传递key参数到sorted方法，来告诉sorted使用哪个键来进行排序。

```
>>> l = [('b',2),('a',1),('c',5),('d',4)]
>>> sorted(l, key=lambda x:x[1])
[('a', 1), ('b', 2), ('d', 4), ('c', 5)]

```

### reverse

用来定义倒序还是正序，默认为False,也就是从小到大排列。

```  
>>> sorted([5,2,3,1,4], reverse=True)
[5, 4, 3, 2, 1]
>>> sorted([5,2,3,1,4], reverse=False)
[1, 2, 3, 4, 5]
```