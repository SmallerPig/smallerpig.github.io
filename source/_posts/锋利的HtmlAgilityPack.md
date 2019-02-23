---
title: 锋利的HtmlAgilityPack
id: 596
categories:
  - 'C#'
date: 2015-04-27 21:52:58
tags:
  - HtmlAgilityPack
---

在开发过程中我们经常需要加载其他网站的内容,或者分析人家网站的数据结构,这时候如果使用原生的xmlReader来做的话会很累,而且效率还不一定高,今天我就来分享一个开源的html解析利器:C#的HtmlAgilityPack

HtmlAgilityPack的官网是[http://htmlagilitypack.codeplex.com/](http://htmlagilitypack.codeplex.com/).在官网上我们可以下载到最新的dll库.

## 先简单介绍XPath

XPath 使用路径表达式来选取 XML 文档中的节点或节点集。节点是通过沿着路径 (path) 或者步 (steps) 来选取的。
下面列出了最有用的路径表达式：
nodename:选取此节点的所有子节点。
/:从根节点选取。
//:从匹配选择的当前节点选择文档中的节点，而不考虑它们的位置。
.:选取当前节点。
..:选取当前节点的父节点。

1、获取网页
``` 
title：doc.DocumentNode.SelectSingleNode("//title").InnerText;
```
解释：XPath中“//title”表示所有title节点。SelectSingleNode用于获取满足条件的唯一的节点。

2、获取所有的超链接：
``` 
doc.DocumentNode.Descendants("a")
```
&nbsp;

3、获取name为kw的input，也就是相当于`getElementsByName\(\)`：
``` 
var kwBox = doc.DocumentNode.SelectSingleNode("//input[@name='kw']");
```
解释："//input[@name='kw']"也是XPath的语法，表示：name属性等于kw的input标签。

关于XPath的完整内容可以参考:[http://www.w3school.com.cn/xpath/](http://www.w3school.com.cn/xpath/ "http://www.w3school.com.cn/xpath/")

## HtmlAgilityPack API简明介绍

&nbsp;

在HtmlAgilityPack中常用到的类有`HtmlDocument`、`HtmlNodeCollection`、`HtmlNode`和`HtmlWeb`等。

经常使用到的方法包括

*   `Load`
*   `SelectNodes`
*   `SelectNodeCollection`
*   `SelectSingleNode`
其流程一般是先获取HTML，这个可以通过HtmlDocument的Load\(\)或LoadHtml\(\)来加载静态内容，或者也可以`HtmlWeb`的Get\(\)或Load\(\)方法来加载网络上的URL对应的HTML。
得到了`HtmlDocument`的实例之后，就可以用`HtmlDocument`的`DocumentNode`属性，这是整个HTML文档的根节点，它本身也是一个`HtmlNode`，然后就可以利用`HtmlNode`的`SelectNodes\(\)`方法返回多个`HtmlNode`的集合对象`HtmlNodeCollection`，也可以利用`HtmlNode`的`SelectSingleNode`\(\)方法返回单个`HtmlNode`。</li>

初始化HtmlDocument类的实例:
``` 
HtmlAgilityPack.HtmlWeb hw = new HtmlAgilityPack.HtmlWeb\(\);
```
加载某一个url :
``` 
HtmlAgilityPack.HtmlDocument doccc = hw.Load(http://www.baidu.com/);
```
获取到某个节点:
``` 
HtmlAgilityPack.HtmlDocument doc= doccc.SelectSingleNode("XPath");
```
获取节点内容可以使用`InnerHTML` 或者`InnerTEXT`方法;

## 编码问题

如果你在获取中文内容的过程中发现了乱码,则需要检查页面的编码,其一般会在页面的`head`标签内:
```
<meta http-equiv="Content-Type" content="text/html; charset=gb2312" />

```
然后在加载该url之前更改HtmlWeb的编码
```
hw.OverrideEncoding = Encoding.Default;//将加载文档的编码改为默认

```