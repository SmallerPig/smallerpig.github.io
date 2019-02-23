---
title: 在Django中使用F\(\)对象
tags:
  - Django
  - python
id: 992
categories:
  - Django
  - python
  - WEB开发
date: 2016-04-21 17:48:17
---

## 问题

F\(\)允许Django在未实际链接数据的情况下具有对数据库字段的值的引用。通常情况下我们在更新数据时需要先从数据库里将原数据取出后放在内存里，然后编辑某些属性，最后提交。例如这样
```
# Tintin filed a news story!
reporter = Reporters.objects.get(name='Tintin')
reporter.stories_filed += 1
reporter.save\(\)


```

上述代码中我们先将reporter.stories_filed的值从数据库中取出放到内存中然后利用python的语法操作计算出新的值之后调用save\(\)方法保存回数据库
最终生成是SQL语句有可能是类似

```  

update reporter set stores_filed = 20 where name = 'smp'; --20可能是经过计算之后得到的值。


```

而我们知道其实我们的需求其实可以理解成SQL：

```  

update reporter set stores_filed = stores_filed+1 where name = 'smp'


```

## 解决

而F\(\)函数的作用就是直接生成SQL语句来描述类似的需求，看下面代码：

```  

from django.db.models import F

reporter = Reporters.objects.get(name='Tintin')
reporter.stories_filed = F('stories_filed') + 1
reporter.save\(\)


```

虽然代码`reporter.stories_filed = F('stories_filed') + 1`有点类似python风格的代码，但实际上却能生成SQL语句直接描述对数据库的操作。

当Django程序中出现F\(\)时，Django会使用SQL语句的方式取代标准的Python操作。
上述代码中不管`reporter.stories_filed`的值是什么，Python都不曾获取过其值，python做的唯一的事情就是通过Django的F\(\)函数创建了一条SQL语句然后执行而已。

需要注意的是在使用上述方法更新过数据之后需要重新加载数据来使数据库中的值与程序中的值对应：

```  

reporter = Reporters.objects.get(pk=reporter.pk)


```

或者使用更加简单的方法：

```  

reporter.refresh_from_db\(\)


```

## 更新多条数据

与上面的在一个对象中操作的例子相似，F\(\)函数同样可以使用在查询集中，配合update\(\)方法。这样我们同样可以将更新的两步合并成一步：

```  

reporter = Reporters.objects.filter(name='Tintin')
reporter.update(stories_filed=F('stories_filed') + 1)


```

我们同样可以使用`update\(\)`方法增加或者更改字段的值，这样做的效率比将其取到内存中后再一个个计算值再更新数据库的效率会提高非常多。

```  

Reporter.objects.all\(\).update(stories_filed=F('stories_filed') + 1)


```

## 组合使用

F\(\)函数可以在创建模型时根据已知的N个字段组合出另外的字段数据，看下面的例子：

```  

company = Company.objects.annotate(
    chairs_needed=F('num_employees') - F('num_chairs'))


```

但是如果组合的两个字段拥有不同的数据类型，那么咱们需要手动告诉Django新生成的数据类似是什么，看下面的列子：

```  

from django.db.models import DateTimeField, ExpressionWrapper, F

Ticket.objects.annotate(
    expires=ExpressionWrapper(
        F('active_at') + F('duration'), output_field=DateTimeField\(\)))
```
上述代码描述了个过期时间的概念，数据库中保存的是激活时间和持续时间，并且通过参数`output_field`告诉`expires`的类型为`DateTimeField`