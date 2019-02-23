---
title: phpstrom 中添加 code snippet
tags:
  - code snippet
  - phpstrom
id: 809
categories:
  - CodeIgniter
  - IDE
date: 2015-07-10 16:16:51
---

我们在编写代码的时候经常会遇到一些代码片段(code snippet),这些代码片段在我们写程序的过程中总是大量的出现,这个时候我们可以将这些代码片段保存起来,然后给这些代码片段定义一个简短的名字,当我们在编辑器中输入这个名字的时候就能直接输出我们定义好的代码片段!

今天来分享在phpstrom中怎样增加我们自定义的代码片段,一般IDE或者编辑器都提供了类似的功能.

我们在写ci程序时经常在View文件里循环输出数组内容,例如官方的文档中循环输出文章标题的例子:

写的多了之后我们发现经常需要用到循环的这段代码,也就是:
```
<?php foreach ($news as $news_item): ?>
    <!--循环内容 -->
<?php endforeach ?>
```
这个时候我们就可以将上面的代码写成一个代码片段以后方便调用.

在File-&gt;Settings-&gt;Editor-&gt;Live Templates-&gt;Zend Html中新建一项,具体参数值可参考图
[![QQ截图20150710161111](http://www.smallerpig.com/wp-content/uploads/2015/07/QQ截图20150710161111.png)](http://www.smallerpig.com/wp-content/uploads/2015/07/QQ截图20150710161111.png)
[![QQ截图20150710161212](http://www.smallerpig.com/wp-content/uploads/2015/07/QQ截图20150710161212.png)](http://www.smallerpig.com/wp-content/uploads/2015/07/QQ截图20150710161212.png)

然后设置最下面一行,Applicable in HTML Text;HTML,PHP.这样我们在这些文件中直接输入代码:hfor,然后按Tab键就可以整个的输出我们需要的代码块,大大的提高了我们的效率!