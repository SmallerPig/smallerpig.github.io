---
title: Django no module named mysql
id: 797
categories:
  - Django
  - python
date: 2015-07-07 00:05:03
tags:
---

使用最新版本的Django(1.8)编写好数据库信息后报上述错误!

修改setting:

'ENGINE': 'mysql'

改为

'ENGINE': 'django.db.backends.mysql'

后正常!

继续执行代码后报错

django.db.utils.OperationalError: (1044, "Access denied for user ''@'localhost' to database 'book'")

可是我明明在setting中使用的是root账户呀!
``` 

 DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.mysql',
        'NAME': 'book',
        'DATABASE_USER': 'root',
        'DATABASE_PASSWORD': '',
        'DATABASE_HOST': 'localhost',
    }
}

 ```
查看官方文档之后发现上述代码不再适用于Django1.8.使用下面代码后一切正常
``` 

 DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.mysql',
        'NAME': 'book',
        'USER': 'root',
        'PASSWORD': '',
        'HOST': 'localhost',
    }
}

 ```