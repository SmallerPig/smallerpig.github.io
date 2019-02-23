---
title: Mac sierra 英文作为系统语言输入中文的一个bug
tags:
  - bug
  - mac
id: 1130
comment: false
categories:
  - 小猪吐槽
date: 2016-11-29 13:58:24
---

## 测试环境

本机环境：mac sierra 10.12.1 macbook pro 2015
语言环境：English（中文环境没有问题）
输入法：系统自带中文或者搜狗拼音输入法，其他中文输入法估计也有问题(未验证)。

## 现象

在任何可输入地方输入汉字，汉字待选项总是两个，如下图：
![出现两个带选项](http://ww3.sinaimg.cn/mw690/88e12591gw1fa8xlxrcq1j21kw0zkwpi.jpg)
通过小猪不断的尝试发现最有可能出现bug的原因是因为与launch pad的配合出现了问题，如最上述图片选中之后到launch pad页面查看会发现如下图：
![选择的中文出现最launch pad的搜索框里](http://ww1.sinaimg.cn/mw690/88e12591gw1fa8xly8thej21kw0zk44o.jpg)
如上图可发现launch pad 的搜索框里出现了刚才我们输入的中文。

这的确是在这个系统下的bug，无奈小猪只好把系统语言切会了中文。