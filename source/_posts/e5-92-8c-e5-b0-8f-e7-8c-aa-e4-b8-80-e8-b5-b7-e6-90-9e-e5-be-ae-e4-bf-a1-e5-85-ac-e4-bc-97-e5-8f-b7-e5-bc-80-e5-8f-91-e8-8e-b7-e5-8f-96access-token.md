---
title: 和小猪一起搞微信公众号开发—获取Access_token
id: 366
categories:
  - 微信开发
date: 2013-12-05 13:51:19
tags:
---


## 前言
前一篇小猪和大家分享了如何回复用户的简单文本，这一篇我们来看看如何获取Access_token

## 介绍

在前一篇中，我们实现了这么一个简单的过程：用户发送一个文本到公众号后，公众号在该文本后面加上点内容返回给用户，这样一个过程的整个开始是由用户来发起的。**用户=》微信服务器=》公众号服务器=》微信服务器=》用户**

但如果我只是由公众号服务器发起一个请求来请求设置，<span style="color: rgb(255, 0, 0); font-family: 'Microsoft Yahei'; font-size: 14px; line-height: 24px; text-indent: 28px;">公众号服务器=》微信服务器=》公众号服务器&nbsp;。</span>

那么这个时候微信服务器怎么来做验证该请求是合法的呢？答案就是在请求参数里面加上Access_token。

例如：在开发者模式下定义自定义菜单：该接口就是由公众号服务器发起(其实可以任何地方发出)给微信服务器，这个过程并没有微信用户参与。

又例如：获取公众号有哪些人关注，这个接口也不是由用户发出。

## 正题

<span style="line-height: 1.6em;">access_token是公众号的全局唯一票据，公众号调用各接口时都需使用access_token。正常情况下access_token有效期为7200秒，重复获取将导致上次获取的access_token失效。</span>

公众号可以使用AppID和AppSecret调用本接口来获取access_token。AppID和AppSecret可在开发模式中获得（需要已经成为开发者，且帐号没有异常状态）。注意调用所有微信接口时均需使用https协议。

### 接口调用请求说明

http请求方式: GET

https://api.weixin.qq.com/cgi-bin/token?grant_type=client_credential&amp;appid=APPID&amp;secret=APPSECRET

### 返回说明

正常情况下，微信会返回下述JSON数据包给公众号：

```
{"access_token":"ACCESS_TOKEN","expires_in":7200}

 
```


错误时微信会返回错误码等信息，JSON数据包示例如下（该示例为AppID无效错误）:

```
{"errcode":40013,"errmsg":"invalid appid"}

 
```

### 全局返回码说明

[全局返回码说明](http://mp.weixin.qq.com/wiki/index.php?title=%E5%85%A8%E5%B1%80%E8%BF%94%E5%9B%9E%E7%A0%81%E8%AF%B4%E6%98%8E"%E5%85%A8%E5%B1%80%E8%BF%94%E5%9B%9E%E7%A0%81%E8%AF%B4%E6%98%8E")

公众号每次调用接口时，可能获得正确或错误的返回码，开发者可以根据返回码信息调试接口，排查错误。

## 另外：调用接口限制

公众号调用接口并不是无限制的。为了防止公众号的程序错误而引发微信服务器负载异常，默认情况下，每个公众号调用接口都不能超过一定限制，当超过一定限制时，调用对应接口会收到如下错误返回码：

```
{"errcode":45009,"errmsg":"api freq out of limit"}

 

```

[限制列表请点击](http://mp.weixin.qq.com/wiki/index.php?title=%E6%8E%A5%E5%8F%A3%E9%A2%91%E7%8E%87%E9%99%90%E5%88%B6%E8%AF%B4%E6%98%8E "%E6%8E%A5%E5%8F%A3%E9%A2%91%E7%8E%87%E9%99%90%E5%88%B6%E8%AF%B4%E6%98%8E")