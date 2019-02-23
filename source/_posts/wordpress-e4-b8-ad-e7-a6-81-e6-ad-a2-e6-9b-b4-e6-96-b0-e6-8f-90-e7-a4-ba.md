---
title: wordpress 中禁止更新提示
id: 398
categories:
  - HTML
date: 2014-01-15 14:54:02
tags:
---

## 前言：

在此之前每每打开blog的时候总是有那么个数字在那边显示，如果是很重要的更新显示在那也就算了，有时候就算一个破主题他还一直在那边，很是让小猪纠结。最关键的是要是更新了主题，那么之前所有自定义的样式以及针对主题定制的代码都会被覆盖，那是极蛋疼的。不更新的话又有个数字在那总是感觉不爽，这是强迫症啊！所以果断百度之：

## 正文：

禁止，插件更新，主题更新，wordpress本身更新提示的方法

进入后台进入编辑页，打开使用的function.php文件。把下列代码加入到文件的最后面。

禁止wp更新
```
//关闭核心提示 add_filter('pre_site_transient_update_core',    create_function('$a', "return null;")); 
//关闭插件提示add_filter('pre_site_transient_update_plugins', create_function('$a', "return null;")); 
//关闭主题提示add_filter('pre_site_transient_update_themes',  create_function('$a', "return null;")); 

//禁止Wordpress检查更新 remove_action('admin_init', '_maybe_update_core');    
//禁止Wordpress更新插件 remove_action('admin_init', '_maybe_update_plugins'); 
//禁止Wordpress更新主题remove_action('admin_init', '_maybe_update_themes');
```
&nbsp;