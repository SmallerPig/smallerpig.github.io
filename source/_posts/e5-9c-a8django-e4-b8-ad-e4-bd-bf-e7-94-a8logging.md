---
title: 在Django中使用logging
tags:
  - Django
  - logging
id: 1022
categories:
  - Django
  - python
date: 2016-05-13 17:48:45
---

## 前言

我们在编写程序过程中，很难免的会出现一些问题，程序并非按照我们预想的那样运行，这个时候我们通常会对程序进行调试，来看看到底是哪边出了问题。而程序日志是来帮助我们记录程序运行过程的帮手，善用日志的程序员也就能很快找出自己程序的问题所在从而快速解决问题。
在服务器级别的组件中都有对应的日志文件，例如nginx、uWSGI都会在运行过程中将一些信息写到日志文件中。
django同样提供了类似功能，在默认情况下并不会出现，今天小猪就带大家来看看django的日志功能。

* * *

## logging

django使用python的内置模块logging来管理自己的日志，所以如果之前了解过python的相关内容会对本文章的内容更容易接受。

### 四大组件

logging中四个重要的概念分别是

1.  Loggers
2.  Handlers
3.  Filters
4.  Formatters

#### Loggers

Loggers(日志记录器)，系统中的每一条日志都是由该组件进行记录的，每一个记录器都应该有其自己的名称并标记其最低记录的等级，在python中，定义了如下几个日志等级(其实小猪目前了解的所有程序语言中都是这几个等级)。

*   DEBUG:所有等级中最低，其信息一般用来作为调试的辅助信息
*   INFO:程序的一般信息
*   WARNING:警告，通常用来对某些可能出现的错误且不会影响程序正常运行的信息进行警告。
*   ERROR:错误，表示出现了某种错误。
*   CRITICAL:奔溃，出现了严重级别的错误。

日志文件中的每一条信息都是一条信息记录，都应该有其对应的级别，然后记录一些打印该条日志时的一些元数据，来告诉查阅者出现这条日志时程序的大致情况，例如调用堆栈或者状态码等等。
当一条日志给到logger时，logger会对该条信息的级别与自身的级别进行比较，如果该日志级别不低于本身级别，logger就会进行下一步操作。相反，如果日志的信息级别比logger的低，那么其会忽略这条日志，不进行任何操作。
当logger经过级别的比较之后决定要对某条日志进行处理时，就将该条日志交给了Handlers

#### Handlers

Handlers(处理器):他来处理具体每条信息，例如是将日志打印到屏幕还是记录到文件或者发送至某个网络连接。
和记录器(logger)一样，Handlers也有自己的记录级别，如果日志级别低于handler的级别，handler同样会忽略掉该条日志。
更贱方便的是:一个logger可以拥有多个handler，而且不同的handler可以拥有不同的记录级别。这样，我们就可以根据不同的日志级别对同一个logger做不同的事情，例如我们可以将级别为WARNING和ERROR的日志打印到屏幕，而级别为CRITICAL级别的日志直接发送到管理者邮箱。

#### Filters

Filter(过滤器):提供了传递给handler之前的附加功能。在通常情况下，一条日志信息只要达到logger的级别之后就会传递给handler处理，但是我们可以通过使用filter来对日志进行额外的过滤。例如我们可以使用某个filter来控制只允许某个特定的源的ERROR级别的日志。
Filter还运行咱们在处理之前修改日志，例如降低或者提高日志的级别。
Filter可以在logger和handler中同时使用，而且多个filter会同时工作。

#### Formatters

Formatter(格式化):定义了怎么显示内容，因为最终的日志都会是以文本的形式展现，formatter就是描述怎么来做这件事。formatter通常都是使用python格式化字符串的方法来对日志进行格式化，具体的属性列表可参考[官方文档](https://docs.python.org/3/library/logging.html#logrecord-attributes)

* * *

## 使用logging

一旦我们配置好对应的loggers, handlers, filters,和formatters之后，我们就可以在我们的代码中使用logging了，使用logging非常的简单，例如下面的代码:
```
# import the logging library
import logging

# Get an instance of a logger
logger = logging.getLogger(__name__)

def my_view(request, arg1, arg):
...
if condition:
# Log an error message
logger.error('Something went wrong!')


```

如上代码，只要condition条件成立，就会调用名为**name**的记录器并且记录一条错误日志。

### 给loggers命名

我们可以使用代码`logging.getlogger\(\)`来生成一个具体名称的logger，所以我们有必要给每一个logger命名。合理的命名logger会给我们带来很大的方便。
为了方便起见，默认情况下python会使用当前模块的**name**命名当前logger，这样我们就可以利用filter根据不同的模块进行处理。我们也可以自己配置自己的logger名称，例如使用符号`.`来组织我们的logger

```  

#get an instance of a specific named logger
logger = logging.getlogger('project.smallerpig.app')


```

我们使用符号`.`来标示logger的层次结构，名称为project.smallerpig的logger通常表示为project.smallerpig.app的上级logger、名称为project的logger表示project.smallerpig和project.smallerpig.app的上级。利用这个特性，我们可以定义一个树结构的logger层，例如定义一个名称为project的根logger，然后在其节点下分别定义smallerpig或者bigerpig的logger，然后再拓展下去。这样我们就可以使用一个project的logger获取该节点下的所有日志信息，并且也可以使用具体的logger只获取部分我们想要的信息。
当然，如果我们不想使用这些设置，我们可以将其关闭。

### 调用logging

每一个logger实例拥有与之对应级别的方法，调用这些方法就可以传递对应级别的日志信息。

*   logger.debug\(\)
*   logger.info\(\)
*   logger.warning\(\)
*   logger.error\(\)
*   logger.critical\(\)

另外，还有两个特殊的方法可以调用:

*   logger.log\(\) 利用特定的级别产生一条日志信息。
*   logger.excetion\(\)，创建一条级别为ERROR的信息，并且包含当前的调用栈。

* * *

## 配置logging

除了仅仅在代码中调用logger的几个产生日志条目的方法，我们还可以对loggers, handlers, filters和formatters进行配置以确保输出的数据是我们想要的。
python的默认logging模块提供了很多种方式来配置，django默认使用字典方式来配置logging。在配置文件中，我们使用变量LOGGING设置具体的loggers, handlers, filters和formatters。并且在缺省值时，默认提供了[默认日志设置](https://docs.djangoproject.com/en/1.9/topics/logging/#default-logging-configuration)
logging设置是在Django的setup\(\)方法中进行的，所以当我们在项目的setting.py文件中设置好之后可确认我们所设置的logger在django程序运行过程中是随时可用的。

### 例子

[dictConfig format的官方文档](https://docs.python.org/3/library/logging.config.html#logging-config-dictschema)中我们可以查阅到所有可设置的项，但在这里小猪使用django官网的例子来说明怎么配置logging。
代码1:

```  

LOGGING = {
    'version': 1,
    'disable_existing_loggers': False,
    'handlers': {
        'file': {
            'level': 'DEBUG',
            'class': 'logging.FileHandler',
            'filename': '/path/to/django/debug.log',
        },
    },
    'loggers': {
        'django': {
            'handlers': ['file'],
            'level': 'DEBUG',
            'propagate': True,
        },
    },
}


```

将上述代码中的filename项的值设置成对应的值之后我们就可以使用了。

代码2:

```  

import os

LOGGING = {
    'version': 1,
    'disable_existing_loggers': False,
    'handlers': {
        'console': {
            'class': 'logging.StreamHandler',
        },
    },
    'loggers': {
        'django': {
            'handlers': ['console'],
            'level': os.getenv('DJANGO_LOG_LEVEL', 'DEBUG'),
        },
    },
}


```

使用上述配置之后并且在环境变量DEBUG=True时，我们可以在控制台中看到所有调试信息，包括django的ORM生存的SQL语句。这在我们调试程序时是非常有用的。
稍微复杂点的例子，代码3:

```  

LOGGING = {
    'version': 1,
    'disable_existing_loggers': False,
    'formatters': {
        'verbose': {
            'format': '%(levelname)s %(asctime)s %(module)s %(process)d %(thread)d %(message)s'
        },
        'simple': {
            'format': '%(levelname)s %(message)s'
        },
    },
    'filters': {
        'special': {
            '\(\)': 'project.logging.SpecialFilter',
            'foo': 'bar',
        },
        'require_debug_true': {
            '\(\)': 'django.utils.log.RequireDebugTrue',
        },
    },
    'handlers': {
        'console': {
            'level': 'INFO',
            'filters': ['require_debug_true'],
            'class': 'logging.StreamHandler',
            'formatter': 'simple'
        },
        'mail_admins': {
            'level': 'ERROR',
            'class': 'django.utils.log.AdminEmailHandler',
            'filters': ['special']
        }
    },
    'loggers': {
        'django': {
            'handlers': ['console'],
            'propagate': True,
        },
        'django.request': {
            'handlers': ['mail_admins'],
            'level': 'ERROR',
            'propagate': False,
        },
        'myproject.custom': {
            'handlers': ['console', 'mail_admins'],
            'level': 'INFO',
            'filters': ['special']
        }
    }
}
```
上述代码中分别对了logging做了一下的设置

*   定义配置信息使用dictConfigversion 1格式。
*   定义了两个格式化器(formatters):

1.  simple，只输出日志级别和日志信息
2.  verbose，输出稍微复杂点的信息，包括:等级、日志信息、附加时间、进程号等等

*   定义两个过滤器(filters):

1.  project.logging.SpecialFilter，使用别名special，如果这个过滤器需要附加参数，它可以提供一个配置字典，在本例中，在实例化SqecialFilter时参数foo会被赋值为bar
2.  django.utils.log.RequireDebugTrue，定义只有在环境变量DEBUG等于True时才输出日志。

*   定义两个处理器(handlers):

1.  console，直接输出级别为DEBUG(或更高)的信息到控制台，本handler使用名称为simple的format
2.  mail_admins，使用AdminEmailHandler发送级别为ERROR(或更高)的信息到网站管理员邮箱，这个handler使用名称为special的过滤器

*   定义三个日志记录器(loggers):

1.  django，打印所有信息到名称为console的handler。
2.  django.request，传递ERROR级别的信息到名为mail_admins的handler，另外的，该条记录器标示了如果使用本记录器处理日志，那么将不使用django处理。
3.  myproject.custom，传递所有INFO级别(或更高)信息到名称为special的过滤器和两个handler。这样就实现了如果是INFO级别的信息则打印到控制台，如果是ERROR或者CRITICAL级别的则通过email发送给管理员。

### 自定义logging配置

### 关闭logging配置

* * *

## Django的 logging拓展