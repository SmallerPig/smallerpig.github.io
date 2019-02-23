---
title: 从内部类中访问本地变量; 需要被声明为final类型
tags:
  - java
id: 1049
categories:
  - java
  - 程序语言
date: 2016-07-26 15:18:58
---

在使用java编写程序时，经常使用接口。当我们想使用接口时通常是直接实例化某一个接口。如下代码：
``` java
public void setCallback(String parm){

    asyncClient.setCallback(new Callback() {
        @Override
        public void messageArrived() {
            System.out.println("parm")
        }
    });
}


```

上述代码在接受到parm参数时调用asyncClient的设置回调方法，最关键看实现的内部使用了parm这个属于外部的变量。
如果我们尝试编译程序，在java 7环境下会报错：

> 从内部类中访问本地变量parm; 需要被声明为`final`类型

在java8环境下可以正常编译通过。
最终的解决方法是在方法中将该参数定义为final类型，即如下代码：

```  java

public void setCallback(final String parm){

    asyncClient.setCallback(new Callback() {
        @Override
        public void messageArrived() {
            System.out.println("parm")
        }
    });
}
```
这样之后调用者也不需要知道具体细节，直接传递指定参数即可。
具体为什么java语言是这样，在谷歌随便搜索了一下就会出现很多结果：
参考1：[Cannot refer to a non-final variable inside an inner class defined in a different method](http://stackoverflow.com/questions/1299837/cannot-refer-to-a-non-final-variable-inside-an-inner-class-defined-in-a-differen)
参考2：[为什么必须是final的呢？](http://cuipengfei.me/blog/2013/06/22/why-does-it-have-to-be-final/)