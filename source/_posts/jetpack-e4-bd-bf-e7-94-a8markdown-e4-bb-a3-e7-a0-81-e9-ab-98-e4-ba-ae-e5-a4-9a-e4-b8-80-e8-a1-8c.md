---
title: Jetpack使用markdown代码高亮多一行
tags:
  - wordpress
id: 1005
categories:
  - WEB开发
date: 2016-05-05 22:16:10
---

小猪的博客站点是使用wordpress搭建的。
为了使用markdown语法发布文章，小猪安装了Jetpack 插件，结合Crayon Syntax Highlighter代码高亮插件，可以让我很方便的使用markdown来编写文章。这两个插件的使用方法这里就不介绍了，今天分享解决使用这两个插件导致生成代码段多出最后一行的解决方法。

## 问题

结合上面所说的插件之后，我们发现会出现如下的问题，
![使用脚本前](http://ww2.sinaimg.cn/mw690/88e12591gw1f3kmztfzc4j20ht0lhn1q.jpg)
由上图可以看到每次使用类似markdown语法```生成代码块时，jetpack插件在md语法转换成html过程中会自动多转义一行。
小猪在Google上面也没找到对应的解决办法，只好自己动手写个脚本来在页面每次加载的时候手动删除了。

## 解决办法

思路非常简单，就是利用js找到每个代码块的最后一行将其删除，我们分析Crayon Syntax Highlighter生成的html代码发现其固定的规律是每一个代码块都会有固定的样式，找到对应的node之后判断最后一行是不是只有一个子元素，如果是的话就将其删除。如果我们设置了Crayon Syntax Highlighter自动显示行好的话再找到对应行好将其删除就OK了，完整的js代码如下:

    var nodes = document.querySelectorAll('.crayon-pre');

    if (nodes.length&gt;1) {
        for (var i = nodes.length - 1; i &gt;= 0; i--) {
            var lastnode = nodes[i].lastChild;
            if (lastnode.childNodes.length==1) {
                nodes[i].removeChild(lastnode)

                var parent = nodes[i].parentNode.parentNode.parentNode.firstChild.childNodes[1].childNodes[1];
                var parentLastNode = parent.lastChild;
                parent.removeChild(parentLastNode);
            };
        };    
    };

然后将这段js代码放到我们的页面中，通常我们使用的主题都会提供footer文件，例如小猪使用的自带的advent主题就提供了类似的文件:

![插入脚本](http://ww4.sinaimg.cn/large/88e12591gw1f3kmzscc7xj20vg0eitdt.jpg)

插入之后清除下缓存看下效果吧!

![插入脚本后](http://ww3.sinaimg.cn/large/88e12591gw1f3kmzt43msj20op0hh0wn.jpg)
同样的文章，效果感觉好多了有么有。

## 最后

这只是小猪临时的解决办法，如果Crayon Syntax Highlighter插件更新之后的解析html代码发生了变化的话那么这段脚本就会失效，或者如果你没有开启Crayon Syntax Highlighter插件的现实代码行数功能，小猪也不保证代码的可运行。所以如果你有更好的方法欢迎您在下面的评论区给出来哦。