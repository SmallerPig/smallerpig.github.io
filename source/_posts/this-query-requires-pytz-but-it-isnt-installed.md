---
title: 'This query requires pytz, but it isn''t installed.'
id: 931
categories:
  - Django
date: 2015-11-10 17:34:17
tags:
---

使用的是Django 1.8，按照官方的教程上面敲代码。为了体验下Django并没有使用mysql数据库而是使用的sqlite3.在使用后台的时候报错标题的错误。
解决方案有两个
1\. 使用pip安装Pytz `$ pip install pytz` 并重启服务
2\. 在设置中将`USE_TZ= False`

具体关于该值的解释请参考[Time_Zone](https://docs.djangoproject.com/en/1.8/ref/settings/#time-zone)