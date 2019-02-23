---
title: 使用了Windows Live Writer 写的博客
id: 392
categories:
  - CSS
  - HTML
  - WEB开发
date: 2014-01-14 15:31:00
tags:
---

&lt;为什么标签不能正确的显示&gt;

重新设置了之后再看看

停用了一些插件！

![](http://clamis.net/wp-content/uploads/2010/10/wlw_wrong_thumb.png)​

偶然看到很多Blog都在说：&ldquo;尝试连接到您的日志时出错:服务器响应无效 &ndash; 从日志服务器接收的对 blogger.getUsersBlogs 方法的响应无效:Invalid response document returned from XmlRpc server必须先纠正此错误才能继续操作。&rdquo;这个错误，好像跟我的有几分相似。都是说wordpress返回的的XmlRpc无法被wlw识别。可具体是那个部分不对却没给提示。

他们说这是因为wordpress本身的一个bug。在utf-8编码下，xml-rpc返回的格式不正确，缺了三个字节，所以wlw就会提示出错。解决的方法是找到博客目录的wp-includes下的class.ixr.php，然后用一个文本编辑工具打开它，查找：

$length = strlen($xml);

替换为：

$length = strlen($xml)+3;

我按照这个方法修改之后就真的没问题了&middot;&middot;看来这个Bug影响不小啊。