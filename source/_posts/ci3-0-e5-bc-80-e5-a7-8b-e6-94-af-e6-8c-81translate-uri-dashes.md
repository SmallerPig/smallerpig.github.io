---
title: CI3.0开始支持translate_uri_dashes
tags:
  - CI
id: 665
categories:
  - CodeIgniter
  - PHP
  - WEB开发
date: 2015-05-06 11:29:26
---

在升级了CI3.0之后我们会发现在application\config的routes.php文件下多了一行:
```
$route['translate_uri_dashes'] = FALSE;

 
```
因为默认的php类名或者方法名是不支持使用破折号的(-)但是在url中却可以使用.例如下列url是完全合法的”

[www.example.com/translate-uri-dashes/test-test](http://www.example.com/translate-uri-dashes/test-test)

但是试想一下我们在CI里面却没有一个很简单的方法来设置这样的url(用正则匹配其中一些关键词?).
从CI3.0开始.为了使使用破折号的url能够正常访问,需要将该参数设置成TRUE,
在设置成TRUE以后,当你访问类似my-controller/index的url时,CI会将其翻译成my_controller/index

Examples:

my-controller/index -> my_controller/index

my-controller/my-method -> my_controller/my_method