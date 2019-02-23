---
title: 本地调试WordPress计划终告失败
id: 403
categories:
  - 小猪吐槽
  - 小猪的感悟
  - 未分类
date: 2014-01-18 21:21:54
tags:
---

小猪本来想把博客的网站数据迁移到自己的电脑上面，mysql数据库还是放在主机供应商，这样就能缓解一下每次写博客时访问速度捉急的状况。

计划是美满的，但是只到实施的时候才发现各种问题。先是直接运行程序时提示 建立数据库连接时出错 。这一定是数据库连接的问题嘛，首先小猪想到的是之前的数据库链接是用localhost来链接的，果断把其改成[www.smallerpig.com](http://www.smallerpig.com) ,可问题还是依旧。然后通过打开调试模式来看看具体情况。
```
/**
 * 开发者专用：WordPress 调试模式。
 *
 * 将这个值改为“true”，WordPress 将显示所有用于开发的提示。
 * 强烈建议插件开发者在开发环境中启用本功能。
 */
define('WP_DEBUG', false);
```
&nbsp;

再次打开时报这个错误：
```
PHP Warning:  mysql_connect\(\):  in E:Studyphppublic_htmlwp-includeswp-db.php on line 1142
PHP Stack trace:
PHP   1\. {main}\(\) E:Studyphppublic_htmlindex.php:0
PHP   2\. require\(\) E:Studyphppublic_htmlindex.php:17
PHP   3\. require_once\(\) E:Studyphppublic_htmlwp-blog-header.php:12
PHP   4\. require_once\(\) E:Studyphppublic_htmlwp-load.php:29
PHP   5\. require_once\(\) E:Studyphppublic_htmlwp-config.php:89
PHP   6\. require_wp_db\(\) E:Studyphppublic_htmlwp-settings.php:72
PHP   7\. wpdb-&gt;__construct\(\) E:Studyphppublic_htmlwp-includesload.php:334
PHP   8\. wpdb-&gt;db_connect\(\) E:Studyphppublic_htmlwp-includeswp-db.php:540
PHP   9\. mysql_connect\(\) E:Studyphppublic_htmlwp-includeswp-db.php:1142
```
&nbsp;

打开wp-admin/index.php页面也出现下面状况

# Error establishing a database connection

This either means that the username and password information in your wp-config.php file is incorrect or we can't contact the database server at www.smallerpig.com . This could mean your host's database server is down. 

Are you sure you have the correct username and password?
Are you sure that you have typed the correct hostname?
Are you sure that the database server is running?

If you're unsure what these terms mean you should probably contact your host. If you still need help you can always visit the http://wordpress.org/support/

首先：1我的用户名和密码肯定不会有问题。

2：我的数据库主机名称也在上面那一步修改正确了吧，应该……

3：不确定数据库是否在running呢。但是如果不running的话我的博客也应该打不开啊。

所以我猜是mysql不支持或者默认不支持这样的操作，果然，在网上找到这样的一篇文章：

[http://cpanel.hostucan.cn/manage-mysql-database-remotely-on-cpanel/](http://cpanel.hostucan.cn/manage-mysql-database-remotely-on-cpanel/ "http://cpanel.hostucan.cn/manage-mysql-database-remotely-on-cpanel/")

接下来我们需要配置工具，推荐使用**Navicat**或者**SQLyog**（如果没有请先下载安装）。这里我们使用Navicat for MySQL 10.0.10版本。使用工具新建一个链接（如图3所示），Connection Name处填写数据库名，其可在cPanel数据库内查看。<span style="color: #ff0000;">在填写HOST时要注意，很多主机默认这一端口是关闭的，这里需要联系你的主机商，要求开启3306端口</span>。全部填写完毕后，点击“**Test Connection**”测试一下链接（如图3所示），如果显示成功的话，点击“**OK**”。

在问了供应商之后果然是这样的。啧啧啧~~~
```
Pig—启  20:55:54
主机的mysql数据库是不是无法在外部进行连接？
胡戈戈  20:55:55
[自动回复]您好，我现在有事不在，一会再和您联系。 不再提醒
胡戈戈  21:01:32
是的
```
这个计划最终就这样夭折了。

还好现在发现了Windows Live Writor！！！