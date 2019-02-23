---
title: 在ASP.NET MVC 中使用MarkDown
tags:
  - MarkDown
id: 870
categories:
  - ASP.NET
  - 'C#'
  - JS
date: 2015-08-21 14:27:56
---

### 首先简单的介绍下Markdown:

markdown技术首先出现在全球最大的程序员问答平台SO上面.由于其简单的语法越来越受到各位的喜欢,由于其让大家在编写文字时能够专注于编写的内容而非编写的排版,所以特别受到一些博客主以及网络写手的钟情!很多编辑器都提供了markdown语法插件来支持它,在github等网站中直接提供了对md文件的直接转换显示.

关于markdown的基本知识也很简单,就是用简单的几个字符来标示不同文字的显示效果.例如,使用#来代表下面的文字为标题

[![image](http://www.smallerpig.com/wp-content/uploads/2015/08/image_thumb.png "image")](http://www.smallerpig.com/wp-content/uploads/2015/08/image.png)

上图中即为使用markdown语法编写的文档.完整的markdown语法可参考[http://daringfireball.net/projects/markdown/syntax](http://daringfireball.net/projects/markdown/syntax "http://daringfireball.net/projects/markdown/syntax")

但是值得注意的一点是:<font color="#ff0000">markdown是语法编辑,而最终呈现在网页端的还是html.</font>想要将markdown语法的内容呈现出来就需要对其进行转换!

有了上面的基础知识以后我们就可以理解:不管使用什么语言,要想使用markdown,无非就是要将使用markdown语法的文档转换成html然后显示出来

今天小猪分享的就是使用C#来对其进行转换!

### C#中使用markdown

好在已经有了第三方开发者为我们提供了这样的类库,[markdownsharp](http://nuget.org/packages/MarkdownSharp)就是其中的一员!在VS的包管理器中可以直接运行下面代码来安装markdownsharp(当然你可以到官网下载对应的dll文件来手动引用)
``` 
>PM> Install-Package MarkdownSharp

 
```

安装好了之后我们就可以在项目中使用markdownsharp.该类也比较简单,我们只需要实例化一个markdown对象之后调用对应的Transform\(\)方法即可,请参考下面代码:

[![image](http://www.smallerpig.com/wp-content/uploads/2015/08/image_thumb1.png "image")](http://www.smallerpig.com/wp-content/uploads/2015/08/image1.png)

### ASP.NET MVC中使用markdownsharp

知道怎么在C#中使用后我们到ASP.NET MVC中就简单多了.

为了简单起见我们直接在cshtml文件中编写了代码:

[![image](http://www.smallerpig.com/wp-content/uploads/2015/08/image_thumb2.png "image")](http://www.smallerpig.com/wp-content/uploads/2015/08/image2.png)

可见我们最终实现了在ASP.NET MVC中使用markdown技术.

### 接下来?

知道上面知识之后我们应该考虑怎么编写我们的markdown文档了.现在很多的编辑器都直接或者间接的支持markdown的语法,我们只需要将对应的文档存入数据库之后然后在前台显示就可.这里有两种做法,

1.直接在存入数据库之前就将其进行转换,这样存入数据库的其实就是html文件.

2.存入数据库markdown语法文件,而到页面显示的时候转换成Html.

这里小猪更建议使用第二种方法,这种方式可以多次编辑,也就是可以将数据库中的文档直接拿出来编辑,而如果使用的第一种方法的话想要编辑那还得将已经存入数据库的html代码转换成markdown文档.这样其实是多走了一步.