---
title: Jetpack的MarkDown插件支持哪些标签
tags:
  - Jetpack
  - MarkDown
  - wordpress
id: 1161
comment: false
categories:
  - PHP
date: 2017-03-03 10:06:31
---

目录：
[TOC]

markdown在程序员这个职业里面是越来越流行了。其官方网站为：http://daringfireball.net/projects/markdown/

得益于jetpack插件，小猪的wordpress博客站点的很多功能都轻而易举的实现了，包括了对小猪喜欢用的Markdown的支持。所以小猪的博客网站在很早之前就开始使用markdown来编写文章。

但是原生的markdown有很多语法，
在这里小猪另外推荐一个markdown编辑器，[MarkEditor](http://zrey.com/app/markeditor)，使用这个小众的软件来编写Markdown的文章真的是异常的舒爽，相比于一般的在线编辑，这个软件提供了单独的应用程序，好在程序是跨平台的。所以一般小猪是先在MarkEditor上面编辑好文章内容，然后直接到WordPress站点后台发布。
这也就是小猪今天写这篇博客的原因了，从MarkEditor的官网上小猪看到很多炫酷的markdown使用方法，还有类似有道云笔记等工具提供了时序图、甘特图等等语法，这些语法是不是jetpack插件都能很好的支持呢，或者说是在WordPress下能不能正常工作呢？包括数学公式，时序图等一系列复杂的玩法。今天小猪来验证一些。
小猪经常使用的标签包括：

    *斜体标签*
    ** 加粗标签**
    [链接标签](http://www.smallerpig.com)
    ![图片标签](http://www.smallerpig.com/123.jpg)
    # 文章标题
    ## 二级标题
    ### 三级标题
    #### 四级标题
    - 列表项1
    - 列表项2
    &gt; 引用的内容
    --- 
    分割符
行内代码`
    \```
    代码块
    \```


 ```

    上面的代码的实际渲染效果如下：
    _斜体标签_
    ** 加粗标签**
    [链接标签](http://www.smallerpig.com)
    ![图片标签](http://www.smallerpig.com/favicon.ico)

    # 文章标题

    ## 二级标题

    ### 三级标题

    #### 四级标题

*   列表项1
*   列表项2
    > 引用的内容

    * * *

    分割符
行内代码`

```  

 代码块


 ```

    关于jetpack的markdown的说明更多详细信息可以参考：[官方网站](https://jetpack.com/support/markdown/)
    关于wordpress的markdown的说明更多详细信息可以参考：[官方网站](https://en.support.wordpress.com/markdown-quick-reference/)

    ## 复杂的标签

    ### 定义：

    <dl>
    <dt>WordPress</dt>
    <dd>A semantic personal publishing platform</dd>

    <dt>Markdown</dt>
    <dd>Text-to-HTML conversion tool
    从上面的结果来看应该是支持定义标签的。</dd>
    </dl>

    ### 表格

    <table>
    <thead>
    <tr>
      <th>Function name</th>
      <th>Description</th>
    </tr>
    </thead>
    <tbody>
    <tr>
      <td>`help\(\)`</td>
      <td>Display the help window.</td>
    </tr>
    <tr>
      <td>`destroy\(\)`</td>
      <td>**Destroy your computer!**</td>
    </tr>
    </tbody>
    </table>

    ### 流程图

```  

 st: 起始
    register: 注册
    condition: 好人?
    check: 盘查一下
    end: 终了
    st &gt; condition
    condition(y) &gt; register &gt; end
    condition(n) &gt; check &gt; register


 ```

    ### Diagrams

    #### Flow charts

```  

 st=&gt;start: Start
    e=&gt;end
    op=&gt;operation: My Operation
    cond=&gt;condition: Yes or No?

    st-&gt;op-&gt;cond
    cond(yes)-&gt;e
    cond(no)-&gt;op


 ```

    #### Sequence diagrams

```  

 Alice-&gt;Bob: Hello Bob, how are you?
    Note right of Bob: Bob thinks
    Bob--&gt;Alice: I am good thanks!


 ```

```  

 TB - top bottom（自上而下）
    BT - bottom top（自下而上）
    RL - right left（从右到左）
    LR - left right（从左到右）


 ```

```  

 graph TD
        A[Christmas] --&gt;B(Go Shopping)
        B --&gt;C{let me think}
        C --&gt;|One| D[labtop]
        C --&gt;|Two| E[Iphone]
        C --&gt;|Three| F[Car]

### Checkbox

You can use `- [ ]` and `- [x]` to create checkboxes, for example:

*   [ ] Item1
*   [ ] Item2
*   [ ] Item3

可见上述复杂的标签还是支持不够啊，好在平时这些简单的标签对于谢谢blog来说已经足够了。