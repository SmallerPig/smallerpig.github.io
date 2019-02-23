---
title: Visual Studio2012使用技巧
id: 1126
categories:
  - WEB开发
date: 2013-08-23 11:01:40
tags:
---

<div id="cnblogs_post_body">

		工欲善其事，必先利其器。虽然说Vim和Emacs是神器，但是对于使用Visual Studio的程序员来说，我们也可以通过一些快捷键和潜在的一些功能实现脱离鼠标写代码，提高工作效率，像使用Vim一样使用Visual Studio。

		当然，如果想真正像使用Vim一样使用Visual Studio可以安装这个插件：[VsVim](http://visualstudiogallery.msdn.microsoft.com/59ca71b3-a4a3-46ca-8fe1-0e90e3f79329/)，只支持VS2010+。

		<span style="font-size:14px;line-height:1.5;">下面我会总结一些我觉得大家平时可能不怎么知道的但是又很好用的一些VS的快捷键和使用技巧。如果您是大牛那不需要看了，哈哈。个人知识有限如果大家还有什么比较实用的快捷键，欢迎分享。因为不像Vim有Normal，Insert两种模式，所以VS快捷键的特点就是需要很多Ctrl, Shift, Alt的参与。这个缺点就是很可能会跟你电脑上某一些程序的快捷键冲突了。而且不知道为什么Visual Studio在不同电脑上的某一些快捷键有可能是不一样的，所以可能文中会有一些快捷键在你的电脑上无法使用，Google it。我目前用的办法就是将我熟悉的配置同步到所有我使用的Visual Studio中来保证我自己用的各个版本之间的快捷键是一样的。</span>

		这些快捷键咋一看挺难记的，但是我的方法是先将觉得有用的记下来，然后下次要使用到这个功能的时候克制住不要用鼠标，去查一下使用快捷键。这么几次以后你就记住了。

```
## 

	**一、主题**

		你可能会很奇怪为什么第一个居然是这么一个东西。当然是这个啦！我们要整天对着VS写代码，debug，面对VS默认的配色你看久了很无聊有木有？眼睛很难受有木有？选择一个合适自己的主题，既可以保护视力，又可以让自己的心情愉悦，心情好了顺便连工作效率也一起提高了不是很好么！

		如果你还在用默认的主题，赶紧换掉吧。下面推荐一个提供VS配色方案的一个网站：StudioStyles，域名和网站同名：[http://studiostyl.es/](http://studiostyl.es/)。下面是[我使用的主题](http://studiostyl.es/schemes/humane-studio)，我觉得看着很舒服，很和谐。

		![My Visual Studio Theme](http://www.smallerpig.com/wp-content/plugins/wp-ueditor/ueditor/php/upload/91461377227869.jpg)

```
## 

	**二、更有效得使用编辑器**

		这里指的编辑器就是也就是大家写代码的地方。

		<span style="text-decoration:underline;">更有效的剪切板</span>

		1\. 循环剪切板: <kbd>Shift</kbd> + <kbd>Ctrl</kbd> + <kbd>V</kbd> 。在VS中多次复制，其实VS都会保存下来，只需要调用这个快捷键就可以把之前多次的复制记录都粘贴出来。

		2\. 整行剪切：<kbd>Ctrl</kbd> + <kbd>X</kbd>。光标不要选中任何文字，然后按这个快捷键就可以把整行剪切下来。 <kbd>Ctrl</kbd> + <kbd>L</kbd> 同样可以实现整行剪切，使用方法也是一样，区别在于使用Ctrl + X后光标会落于下一行的行尾，二使用<kbd>Ctrl</kbd> + <kbd>L</kbd>光标则会停在下一行的行首。

		3\. 整行复制：<kbd>Ctrl</kbd> + <kbd>C</kbd>。这个和<kbd>Ctrl</kbd> + <kbd>X</kbd>的使用方法一样。

		&nbsp;

		<span style="text-decoration:underline;">更有效的选择：</span>

		1\. 基本选择：<kbd>Shift</kbd> + 光标（<kbd>&larr;</kbd><kbd>&darr;</kbd><kbd>&uarr;</kbd><kbd>&rarr;</kbd>） &nbsp;。基于光标所在的地点，按住<kbd>Shift</kbd>然后使用上下左右光标可以自由选择。

		2\. 基于单词选择：<kbd>Shift</kbd> + <kbd>Ctrl</kbd>+（<kbd>&rarr;</kbd><kbd>&larr;</kbd>）。使用这个可以跳跃单词的选，也可配合<kbd>Home</kbd>/<kbd>End</kbd>选择整行

		3\. 基于&ldquo;方块&rdquo;选择：<kbd>Shift</kbd> + <kbd>Alt</kbd> + (<kbd>&larr;</kbd><kbd>&darr;</kbd><kbd>&uarr;</kbd><kbd>&rarr;</kbd>) 或者<kbd>Alt</kbd> + 鼠标。

		4\. 选择一个整个单词：<kbd>Shift</kbd> + <kbd>Ctrl</kbd> + <kbd>W</kbd>。把光标放在某个单词中的时候按快捷键即可。

		&nbsp;

		<span style="text-decoration:underline;">更有效的编辑：</span>

		1\. 整行删除：<kbd>Shift</kbd> + <kbd>Delete</kbd>。

		2\. 删除下一个单词：<kbd>Ctrl</kbd> + <kbd>Delete</kbd>。

		3\. 删除上一个单词：<kbd>Ctrl</kbd> + 退格（<kbd>Backspace</kbd>）

		&nbsp;

		<span style="text-decoration:underline;">更有效的位置跳转：</span>

		1\. 基于单词的跳转：<kbd>Ctrl</kbd> + (<kbd>&larr;</kbd><kbd>&rarr;</kbd>)。此快捷键可以让光标以单词为单位左右进行跳转。

		2\. 跳到上一个本单词： &nbsp; <kbd>Shift</kbd> + <kbd>Ctrl</kbd> +（<kbd>&darr;</kbd><kbd>&uarr;</kbd>） &nbsp;。这个功能比较有用，可以将光标移动到光标所在的那个单词上次或者下次在文中出现的地方。

		3\. 跳到上一个光标停留的地方： &nbsp; <kbd>Ctrl</kbd> + <kbd>-</kbd>（往前）；<kbd>Ctrl</kbd> + <kbd>Shift</kbd> + <kbd>-</kbd> &nbsp;（往后）

		4\. 快速跳转到某一行： <kbd>Ctrl</kbd> + <kbd>G</kbd>

		5\. 快速跳到文件头尾：<kbd>Ctrl</kbd> + <kbd>Home</kbd>/<kbd>End</kbd>

		6\. 快速跳转到本行第一个非空格开头：<kbd>Home</kbd>。如果要到本行最开头则按两下<kbd>Home</kbd>即可。

		7\. 快速跳转到本行结尾：<kbd>End</kbd>

		8\. 匹配括号移动：<kbd>Ctrl</kbd> + <kbd>]</kbd>，适用于 <kbd>(</kbd><kbd>)</kbd>, <kbd>{</kbd><kbd>}</kbd>, <kbd>[</kbd><kbd>]</kbd>, <kbd>&ldquo;</kbd><kbd>&rdquo;</kbd> 。将光标放在需要匹配的括号然后按这个快捷键，光标会跳转到其相对于那个的括号上去。这个功能比较有用，但是我还有一个建议。Visual Studio本来就会将相对应的括号给特别标识出来，只是一般默认的那个颜色和背景颜色比较类似看不出来，建议将其在Font And Colors中设置成醒目的颜色。那个括号匹配设置如图，中文不知道是什么，大家找一下应该就可以找到了。

		![Color Setting](http://www.smallerpig.com/wp-content/plugins/wp-ueditor/ueditor/php/upload/33521377227869.jpg)

		设置好以后效果如下，是不是很醒目了？这样就可以在括号群中迅速找到和它对应的那一个了。

		![Brace](http://www.smallerpig.com/wp-content/plugins/wp-ueditor/ueditor/php/upload/74281377227869.jpg)

		&nbsp;

```
## 

	**三、继续更有效率的编辑器**

		3.1 更有效的编辑（补充）

		a. 注释代码：<kbd>Ctrl</kbd> + <kbd>E</kbd>(Edit) + <kbd>C</kbd>(Comment)， <kbd>Ctrl</kbd> + <kbd>K</kbd> + <kbd>C</kbd>（Comment）。打开文件类型不同行为可能不同，在cs文件类型中会将选中行的代码注释，cpp中会将选中的内容进行注释。

		&nbsp; 反注释代码：<kbd>Ctrl</kbd> + <kbd>E</kbd>(Edit) + <kbd>U</kbd>(Uncomment)， <kbd>Ctrl</kbd> + <kbd>K</kbd> + <kbd>U</kbd>(Uncomment)

		b. 调整格式选中代码格式：<kbd>Ctrl</kbd> + <kbd>E</kbd>(Edit) + <kbd>F</kbd>(Format)。<span style="color: rgb(255, 0, 0); font-size: 13px;">Ctrl + K +F</span>

		c. 调整整个文档代码格式：<kbd>Ctrl</kbd> + <kbd>E</kbd>(Edit) + <kbd>D</kbd>(Document Format)。 <span style="color:#FF0000;">Ctrl+K +D</span>

		3.2 更有效率的搜索：

		a. Incremental Search（增量搜索，不知道翻译得恰不恰当）：<kbd>Ctrl</kbd> + <kbd>I</kbd>(Incremental) (移动到下一个匹配按<kbd>Ctrl</kbd> + <kbd>I</kbd>, 移动到上一个<kbd>Shift</kbd> + <kbd>Ctrl</kbd> + <kbd>I</kbd>)。按住快捷键然后输入要查询的字符串，VS会马上定位到而不需要想<kbd>Ctrl</kbd> + <kbd>F</kbd>那种确认的过程，可以通过我截的图中看到效果。我一般如果只是想在当前文档进行简单搜索的话一般会使用这个搜索，遇到是一些比较复杂的搜索条件才去动用弹框搜索。

		![Incremental Search](http://www.smallerpig.com/wp-content/plugins/wp-ueditor/ueditor/php/upload/70001377227869.gif "Incremental Search")

		b. <kbd>Ctrl</kbd> + <kbd>F</kbd>(Find)：在Visual Studio 2012中其实这个功能已经和Increment Search很相似了，你会发现在VS2012+里使用<kbd>Ctrl</kbd> + <kbd>F</kbd>和上面的效果是一样的，都是输入即可看到搜索结果。不过与Increment Search不同的是，这个搜索可以指定更多的条件，如是否匹配大小写、是否整词搜索、是否用正则表达式以及搜索的范围。

		c. 在文件中查找：<kbd>Ctrl</kbd> + <kbd>Shift</kbd> + <kbd>F</kbd>，这个可以实现的搜索功能与<kbd>Ctrl</kbd> + <kbd>F</kbd> 一模一样，唯一不同就是这个可以将你搜索的结果输出到查找结果窗口中，而不是一个一个显示出来。这个比较合适搜一些比较多匹配的东西，然后在输出的结果窗口中在肉眼筛选。

```
## 

	**四、更有效的导航：**

		1\. 快速打开Solution Explorer：<kbd>Ctrl</kbd> + <kbd>W</kbd>(Windows)+ <kbd>S</kbd>(Solution)、<kbd>Ctrl</kbd> + <kbd>Alt</kbd> + <kbd>L</kbd>。当你在写代码想打开工程中另一个文件时就可以用这个快速打开解决方案窗口选择文件。

		2\. 打开当前打开文件列表：<kbd>Ctrl</kbd>+<kbd>Alt</kbd>+<kbd>Down</kbd>。这个很好用，但是这个快捷键在很多电脑上都会翻转屏幕，囧。如果实在要用这个功能，可以通过自定义快捷键来实现。

		![File List](http://www.smallerpig.com/wp-content/plugins/wp-ueditor/ueditor/php/upload/2961377227869.png)

		3\. 快速将焦点移到类列表(这个名词纯属YY，见图便知我指的是啥)：<kbd>Ctrl</kbd> +<kbd>F2</kbd>。

		![Class list](http://www.smallerpig.com/wp-content/plugins/wp-ueditor/ueditor/php/upload/44921377227869.png)

		4\. 内部文件切换：<kbd>Ctrl</kbd> + <kbd>Tab</kbd>。这个不仅在VS中，很多软件中都是这个功能。

		5\. 全屏：<kbd>Shift</kbd> + <kbd>Alt</kbd> + <kbd>Enter</kbd>。可以让你进入全屏无干扰模式，本人很喜欢这个功能。

```
## 

	**五、更有效的智能感知**

		智能感知本来就很智能，但是很多时候我们想强制的调出一些提示来看一下的时候这些功能就爽。很多功能语言描述可能比较累，而且由于我语文不好很可能你还看不懂，所以我会附图。

		1\. 列出成员。<kbd>Ctrl</kbd> + <kbd>K</kbd> + <kbd>L</kbd>（List Member）， <kbd>Ctrl</kbd> + <kbd>J</kbd>。我们知道当我们需要访问对象方法的时候按.VS会自动提示出有哪些方法，但是有时候我们需要在.操作符已经存在的情况下再查看。以前我会把点删掉然后再点一次，我承认我当时很傻，后来知道这个快捷键以后就好多了。

		![List Member](http://www.smallerpig.com/wp-content/plugins/wp-ueditor/ueditor/php/upload/76911377227869.gif)

		2\. 列出选项（表述不明确，具体看后面描述）。<kbd>Ctrl</kbd> + <kbd>.</kbd> 或者<kbd>Ctrl</kbd> + <kbd>Shift</kbd> +<kbd>F10</kbd>。当我们用到一些类型在我们工程引用的程序集里但是没有在当前当前文件引用的命名空间内时，或者我们写了一个不存在的函数时，那行代码会有错误提示，并且在左下角有一个小符号。如图：![UnknowType](http://www.smallerpig.com/wp-content/plugins/wp-ueditor/ueditor/php/upload/87461377227869.png)。我们鼠标移到符号附近会出现一些帮助，可以自动帮助我们添加引用或者生成函数。这个快捷键就是在不移动鼠标的情况下让其出现这个提示。

```
## 

	**六、其他**

		1\. 任务列表（Task List），可以通过View-&gt;Task List打开这个窗口。很多人可能不知道这个功能，我觉得挺有用。写代码的时候我往往会遇到这种情况，某一些代码我现在不确定需求或者觉得可能会有问题将来需要改善，我会加上注释：//TODO：reason。相信很多人会有同样的习惯，这个任务列表的功能就是让我们可以看到我们当前工程中有多少个TODO项。当然不局限于TODO这个词，可以自定义词汇。我一般会在commit之前看一下这个列表看看还有没有需要改的地方。这个还可以直接添加一些任务，具体使用自己用一下就知道了。

		![Task List](http://www.smallerpig.com/wp-content/plugins/wp-ueditor/ueditor/php/upload/33351377227869.jpg)

```
## 

	七、插件

		这里在推荐两个插件：C# outline 和Smart Paster。

		1\. [C# Outline](http://visualstudiogallery.msdn.microsoft.com/bc07ec7e-abfa-425f-bb65-2411a260b926)

		Visual Studio默认的outline是只有在函数级别的，但是很多时候有一些循环条件很长也需要缩起来看比较方便。于是就有了这个插件。效果如下：

		Before outline-&gt;![Before outline](http://www.smallerpig.com/wp-content/plugins/wp-ueditor/ueditor/php/upload/26341377227870.jpg)

		After outline-&gt; ![After outline](http://www.smallerpig.com/wp-content/plugins/wp-ueditor/ueditor/php/upload/78081377227870.jpg)

		2\. [Smart Paster](http://smartpaster2010.codeplex.com/)

		这个插件可以将文字粘帖为注释、和string字符串和StringBuilder。特别是对于粘贴多行的文字的时候很有用。

```
## 

	八、推荐资料

		1\. [Favorite Visual Studio keyboard shortcuts](http://stackoverflow.com/questions/98606/favorite-visual-studio-keyboard-shortcuts)：Stackoverflow 上一群人在讨论自己最喜欢的快捷键，可以去里面看看或许你会看到一些你意想不到的快捷键。

		2\. [Visual Studio](http://book.douban.com/subject/4224471/)[ ](http://book.douban.com/subject/4224471/)[程序员箴言](http://book.douban.com/subject/4224471/)：这本书介绍了很多关于VS方面的知识。

		3\. 可以多看看VS菜单栏上那些没用过东西，或许你会发现一些对你很有用的东西。

		&nbsp;

```
## 

	使用注释生成XML注释文档

		实际上该方法适合于所有版本的Visual studio，方法很简单，设置一下Visual studio的项目属性和工具选项即可。

		1.在菜单栏的&ldquo;Project&rdquo;中选择当前项目的&ldquo;*** Properties&rdquo;，然后在&ldquo;Build&rdquo;标签页中找到&ldquo;Output&rdquo;一栏，在&ldquo;XML documentation files&rdquo;复选框中打上勾勾，在自定义输出的XML文档的文件名即可。

		2.检查菜单栏上的&ldquo;Tools&rdquo;中选择&ldquo;Options&rdquo;菜单项，然后在&ldquo;Text Editor&rdquo;支点中找到&ldquo;C#&rdquo;，在其子项中找到&ldquo;Advanced&rdquo;，然后在&ldquo;XML Documentation Comments&nbsp;&rdquo;下的&ldquo;Generate Xml documentation comments for///&rdquo;复选框是否已被选中，如果没被选中就选上它。

		&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 一切工作完成之后就可以生成相应的注释XML文档了。Build一下自己的工程，看看生成的文件吧^^

		&nbsp;

		&nbsp;

		**总结**

		这些只是对我来说最有用的一些技巧，强烈推荐大家可以去看看我推荐的那些资料去探索一些更加适合你的习惯的一些功能。因为当你不知道有这个功能的存在的情况下你根本就想不到要去找这么一个功能。

		如果你需要找一个你不知道的快捷键，可以通过在菜单栏上去看，一般常用的都会将快捷键放在菜单边上。或者你可以去MSDN上去查一下：[http://msdn.microsoft.com/en-us/library/vstudio/dd576362.aspx](http://msdn.microsoft.com/en-us/library/vstudio/dd576362.aspx) 。我觉得非常有必要去看一些类似于高效使用VS的资料，因为很多时候如果你不知道某一些功能的存在，你根本就不会想到去用更别说去搜这个功能。

		_编辑器中还有很多其他的技巧，先写一部分吧，这只是很小的一部分，还有很多其他的技巧以后慢慢道来_。以后可能还会总结一些关于搜索、编辑、调试、导航、Intellisense等等的内容。

		没想到上一篇文章有这么多人喜欢，多谢大家支持。继续～

		很多比较通用的快捷键的默认设置其实是有一些缩写在里面的，这个估计也是MS帮助我们记忆。比如说注释代码的快捷键是<kbd>Ctrl</kbd> + <kbd>E</kbd> + <kbd>C</kbd>，我们如果知道它是 <kbd>Ctrl</kbd> + Edit + Comment Code 的缩写不是更好记么？我也会尽量YY把快捷键和功能联系起来来帮助我自己记忆。另外很多功能在VS中有多个快捷键可以实现，我猜是为了防止一些快捷键冲突所设计的吧，我一般只会去记好记的，冲突了再说。

		&nbsp;

		容我再罗嗦几句。我们绝对没有必要去死记硬背很多很多快捷键然后装逼，因为并非所有的快捷键所有人都需要。很大程度上一些功能的使用是取决于你的工作习惯，同时我也不推荐你去记一些你觉得你都不会用到的快捷键，没意义，浪费时间。这也就是为什么我只列出一些对我工作效率有切身帮助的一些快捷键，而不是把Visual Studio中所有的快捷键都列出来，因为那样子的话就没意义了！

		**所以我强烈建议你们只去记你觉得有用的那些东西。**

* * *

		原文地址：[http://www.cnblogs.com/imjustice/p/3139147.html](http://www.cnblogs.com/imjustice/p/3139147.html)

		&nbsp;

</div>