---
title: Python的if表达式
tags:
  - python
id: 955
categories:
  - python
date: 2015-11-26 10:30:19
---

一般的逻辑判断语句就不解释了
解释下if表达式
代码：

    a, b, c = 1, 2, 3
    c = a if a&gt;b else b
    print(c)


 ```

    最终的结果是2,解释为如果a>b那么c=a，不然就c=b。类似于C#的语法:

```  

 c=a&gt;b?a:b

上面这种语法结果貌似在其他语言中更常见。