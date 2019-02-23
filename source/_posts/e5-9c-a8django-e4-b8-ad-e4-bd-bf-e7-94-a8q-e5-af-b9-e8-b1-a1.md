---
title: 在Django中使用Q\(\)对象
tags:
  - Django
  - python
id: 1000
categories:
  - Django
  - python
  - WEB开发
date: 2016-04-22 16:10:54
---

## 问题

一般我们在Django程序中查询数据库操作都是在QuerySet里进行进行，例如下面代码:
```
&gt;&gt;&gt; q1 = Entry.objects.filter(headline__startswith="What")
&gt;&gt;&gt; q2 = q1.exclude(pub_date__gte=datetime.date.today\(\))
&gt;&gt;&gt; q3 = q1.filter(pub_date__gte=datetime.date.today\(\))


```

或者将其组合起来，例如:

```  

&gt;&gt;&gt;q1 = Entry.objects.filter(headline_startswith="What").exclude(pub_date_gte=datetime.date.today\(\))


```

随着我们的程序越来越复杂，查询的条件也跟着复杂起来，这样简单的通过一个filter\(\)来进行查询的条件将导致我们的查询越来越长。

## Q\(\)对象就是为了将这些条件组合起来。

当我们在查询的条件中需要组合条件时(例如两个条件“且”或者“或”)时。我们可以使用Q\(\)查询对象。
例如下面的代码

```  

from django.db.models imports Q
q=Q(question_startswith="What")


```

这样就生成了一个Q\(\)对象，我们可以使用符号&amp;或者|将多个Q\(\)对象组合起来传递给filter\(\)，exclude\(\)，get\(\)等函数。当多个Q\(\)对象组合起来时，Django会自动生成一个新的Q\(\)。
例如下面代码就将两个条件组合成了一个

```  

Q(question__startswith='Who') | Q(question__startswith='What')


```

使用上述代码可以使用SQL语句这么理解:

```  

WHERE question LIKE 'Who%' OR question LIKE 'What%'


```

我们可以在Q\(\)对象的前面使用字符“~”来代表意义“非”，例如下面代码:

```  

Q(question__startswith='Who') | ~Q(pub_date__year=2005)


```

对应SQL语句可以理解为:

```  

WHERE question like "Who%" OR year(pub_date) !=2005


```

这样我们可以使用 “&amp;”或者“|”还有括号来对条件进行分组从而组合成更加复杂的查询逻辑。

也可以传递多个Q\(\)对象给查询函数，例如下面代码:

```  

News.objects,get(
    Q(question__startswith='Who'),
    Q(pub_date=date(2005, 5, 2)) | Q(pub_date=date(2005, 5, 6))
)


```

多个Q\(\)对象之间的关系Django会自动理解成“且(and)”关系。如上面代码使用SQL语句理解将会是:

```  

SELECT * from news WHERE question LIKE 'Who%'  AND (pub_date = '2005-05-02' OR pub_date = '2005-05-06')


```

Q\(\)对象可以结合关键字参数一起传递给查询函数，不过需要注意的是要将Q\(\)对象放在关键字参数的前面，看下面代码

```  

#正确的做法
News.objects.get(
    Q(pub_date=date(2005, 5, 2)) | Q(pub_date=date(2005, 5, 6)),
    question__startswith='Who')

#错误的做法，代码将关键字参数放在了Q\(\)对象的前面。
News.objects.get(
    question__startswith='Who',
    Q(pub_date=date(2005, 5, 2)) | Q(pub_date=date(2005, 5, 6)))
```