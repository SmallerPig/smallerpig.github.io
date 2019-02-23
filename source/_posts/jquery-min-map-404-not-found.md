---
title: 'jquery.min.map 404 (Not Found) '
id: 297
categories:
  - JS
  - WEB开发
date: 2013-09-03 11:23:09
tags:
---

很高兴你能注意到这个问题，因为其实这个错误对页面呈现效果是毫无影响的。说明你找到这个问题完全是想你的页面完美运行毫无错误！小猪说错了吗？

请回答：

1.  1：你用的是chrome浏览器吗？

2.  2：其他浏览器没出现这个错误吧？

3.  3：你用了jquery库，而且你并没有在页面引用标题中的文件。

回答应该都是“YES”，因为距目前为止好像只有chrome浏览器支持这个特性。

下面小猪来说说这个问题的原因。

目前大多数js库都是使用压缩过的，压缩的好处是：

``` 
（1）压缩，减小体积。比如jQuery 1.9的源码，压缩前是252KB，压缩后是32KB。
（2）多个文件合并，减少HTTP请求数。
（3）其他语言编译成JavaScript。
```

这三种情况，都使得实际运行的代码不同于开发代码，除错（debug）变得困难重重。
通常，JavaScript的解释器会告诉你，第几行第几列代码出错。但是，这对于转换后的代码毫无用处。举例来说，jQuery 1.9压缩后只有3行，每行3万个字符，所有内部变量都改了名字。你看着报错信息，感到毫无头绪，根本不知道它所对应的原始位置。
**这就是Source map想要解决的问题。**

简单说，Source map就是一个信息文件，里面储存着位置信息。也就是说，转换后的代码的每一个位置，所对应的转换前的位置。
有了它，出错的时候，除错工具将直接显示原始代码，而不是转换后的代码。这无疑给开发者带来了很大方便。（下图中，上面的图是经过压缩后的js，下面的图是在调试时浏览器根据对应的map文件将压缩后的js文件还原后的结果）

![](http://www.smallerpig.com/wp-content/plugins/wp-ueditor/ueditor/php/upload/82921378178807.png)

目前，暂时只有Chrome浏览器支持这个功能。在Developer Tools的Setting设置中，确认选中&quot;Enable source maps&quot;。

![](http://www.smallerpig.com/wp-content/plugins/wp-ueditor/ueditor/php/upload/23071378178807.png)

你可以在你的js文件里面找到类似对应行。

```
//@ sourceMappingURL=jquery.min.ma
 ```

**现在知道原因了来看看解决方案：**

1.  直接删除jquery库的对应行文件，即`//@ sourceMappingURL=jquery.min.ma`，这个方法最简单也最直接了。哈哈。

2.  如果你需要继续调试这段js代码的话引用对应的map文件，例如[http://ajax.googleapis.com/ajax/libs/jquery/1.9.0/jquery.min.map](http://ajax.googleapis.com/ajax/libs/jquery/1.9.0/jquery.min.map)

3.  禁用浏览器的该功能，即把图2中的钩钩去掉。

