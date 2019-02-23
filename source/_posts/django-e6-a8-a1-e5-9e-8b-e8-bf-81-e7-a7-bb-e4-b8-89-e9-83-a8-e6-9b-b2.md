---
title: Django模型迁移三步曲
id: 806
categories:
  - Django
date: 2015-07-08 16:09:53
tags:
---

参考自[官方教学文档](https://docs.djangoproject.com/en/1.8/intro/tutorial01/#activating-models)

three-step guide to making model changes:

1.  Change your models (in models.py).
2.  Run python manage.py makemigrations to create migrations for those changes
3.  Run python manage.py migrate to apply those changes to the database. 

印象中asp.net mvc中也提供了类似的方法,可是我并没有使用过!

应该是直接编写模型类,然后数据库表等信息是通过框架给生成的.而一旦数据结构发现变化则需要通过迁移类来完成数据库表结构的变动.而我之前一直都是模型类根据数据库表结构来作调整!

看来code first模式越来越受到大众的认可了!