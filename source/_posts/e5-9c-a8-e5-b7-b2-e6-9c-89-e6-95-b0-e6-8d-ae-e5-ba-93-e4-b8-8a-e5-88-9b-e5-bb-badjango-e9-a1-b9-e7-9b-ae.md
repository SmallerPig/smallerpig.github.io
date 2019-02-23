---
title: 在已有数据库上创建Django项目
tags:
  - Django
id: 1080
categories:
  - Django
date: 2016-08-22 16:17:02
---

## 前言

* * *

其实官方文档中已经给出了具体的步骤，本文只是对官方文档的一个简单的翻译，一切以最新[官方文档](https://docs.djangoproject.com/en/1.10/howto/legacy-databases/)为准。

## 简介

* * *

虽然一般使用Django开发程序时我们通常是新建项目，但也非常有可能是基于已经存在的数据库集成的。Django提供了这种机制来实现这种可能。

## 设置数据库参数

* * *

你需要告诉Django你已经存在的数据库配置参数，具体的我们来设置"Default"值
- Name
- ENGINE
- USER
- PASSWORD
- HOST
- PORT

## 自动生成模型文件

Django提供了`inspectdb`命令来检测已经存在的数据库，并自动生成模型文件，我们可通过下列命令来在命令行中进行查看：
```
$ python manage.py inspectdb


```

当然我们可以通过标准的Unix风格输出(重定向)到指定文件

```  

$ python manage.py inspectdb &gt; models.py


```

适当编辑模型文件之后，把我们的app添加到配置项的`INSTALLED_APPS`中。
默认的，`inspectdb`命令创建了一个`Meta`类属性中`managed = Falsed`的模型。该模型不可通过Django的自带后台管理系统来管理。

```  

class Person(models.Model):
    id = models.IntegerField(primary_key=True)
    first_name = models.CharField(max_length=70)
    class Meta:
        managed = False
        db_table = 'CENSUS_PERSONS'


```

当然，如果我们需要使用Django的后台来管理的话，将`managed = Falsed`删除或者将值设置成`True`即可。

## 安装Django的核心表

* * *

接下来，使用`migrate`命令来安装Django额外需要的表，例如管理员权限和内容类型等。

```  
$ python manage.py migrate
```
## 测试

* * *

然后就是对程序进行开发和测试了
done..