---
title: Server.UrlEncode()方法 空格转换成了+而非%20
id: 438
categories:
  - 'C#'
  - HTML
  - MVC
date: 2014-03-19 16:23:45
tags:
---

在ASP.NET MVC 的Control类里提供了该方法。该方法可以很方便的对字符串进行url编码，但小猪今天却发现其将空格编码后变成了“+”而非JavaScript采用的[encodeURIComponent()](http://www.w3schools.com/jsref/jsref_encodeURIComponent.asp)编码之后的%20。也许这算一个bug也许也不算。仔细想想在我们的url中确实不会存在空格，但是文件系统的命名却是可以使用空格的(Program Files)，所以必须将空格转码。

那为什么在.Net下回转换成+而在js中会是%20呢？关键问题是在encode成+之后再decode却不能转换成了空格了呀。这确实是个蛋疼的问题。引用了老外的一段描述：
> (http://stackoverflow.com/questions/2516266/server-urlencodestring-s-doesnt) As far as I know, historically the "+" has been used in URL encoding as a special substitution for the space char ( ASCII 20 ). If an implementation does not take the space into consideration as a special character with the '+' substitution, then it still has to escape it using its ASCII code ( hence '%20' )
> (http://stackoverflow.com/questions/2516266/server-urlencodestring-s-doesnt) Actually they're both wrong! JavaScript escape() should never be used. As well as failing to encode the + character to %2B, it encodes all non-ASCII characters as a non-standard %uNNNN sequence.  Meanwhile Server.UrlEncode is not exactly URL-encoding as such, but encoding to application/x-www-form-urlencoded, which should only normally be used for query arameters. Using + to represent a space outside of a form name=value construct, such as in a path part, is wrong.  This is rather unfortunate. You might want to try doing a string replace of the + character with %20 after encoding with UrlEncode() when you are encoding into a path part rather than a parameter. In a parameter, + and %20 are equally good.