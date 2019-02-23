---
title: ASP.NET MVC是不是符合MVC思想的框架
tags:
  - MVC
id: 887
categories:
  - 面向对象基础
date: 2015-09-10 23:13:01
---

几年前一开始接触ASP.NET MVC框架的时候就被它所吸引.相对于web form真的是太方便了.完全的逻辑和视图分离!而作为偏服务器端的我不需要过多的前端知识却能和前端无缝对接.简直就是神器嘛.

用了差不多两年了,也陆续接触了CI和Django.发现虽然都是声称符合MVC思想的框架,但是仔细想想还是会发现其中的区别.

其主要区别可能是在Model上面,最熟悉的ASP.NET MVC中,model只是数据模型,而真正呈现的是视图模型.而CI,Django等框架,可能是使用Php,python等弱类型语言的原因.其框架中并没有明确的视图模型的概念.另外最大的区别在于在其他MVC框架中,数据都是从Model直接获取的,也就是说最复杂的逻辑有可能出现在Model里,其担负着与数据库连接,取数据等操作.但是在ASP.NET MVC中,我们所定义的也只能是数据的模型,而不能在模型中定义过多的数据库连接之类.

所以我一直认为ASP.NET MVC因为其model的原因并不算是真正符合MVC思想的.

附上维基百科对MVC的链接[https://zh.wikipedia.org/wiki/MVC](https://zh.wikipedia.org/wiki/MVC "https://zh.wikipedia.org/wiki/MVC")