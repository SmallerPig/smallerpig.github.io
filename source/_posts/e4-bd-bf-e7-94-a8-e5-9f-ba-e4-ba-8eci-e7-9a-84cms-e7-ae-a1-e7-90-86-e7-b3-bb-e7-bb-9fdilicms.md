---
title: 体验基于CI的CMS管理系统DiliCMS
tags:
  - CI
id: 650
categories:
  - CodeIgniter
  - PHP
  - WEB开发
date: 2015-04-29 22:15:47
---

在开发网站过程中我们经常要为网站开发管理网站内容的后台.也就是CMS(内容管理系统)!我们通常都是单独的给每个站都单独开发一个后台,这样做的过程一般都是先根据需求手动建好数据库并建好表!然后根据数据结构开发对应的程序.

这样在开发了很多的网站之后我们会发现基本上所有网站的数据模型都是相似的,例如wordpress就是以文章表为主线的!而一般的新闻发布类网站无非也就是内容表,类别表,作者之类~

这样,我们就可以开发出固定的CMS来管理这些类容…

之前小猪都是这么想的…每次网站或者app的后台都是手动开发.

今天小猪来分享基于CI的DiliCMS,该CMS为纯后台,没有前台,自己可以根据自己需求定制页面部分,更重要的是该系统使用的MIT协议,也就是我们可以完全根据自己的需求来二次开发该CMS.这一点相比过年目前的phpcms来说好多了.后者一开打各种版权问题.!

小猪使用wamp来体验下dilicms.

将下载好的项目代码复制到wamp的www文件夹下.打开localhost后可看到默认首页:该项目的官方网站为:[http://www.dilicms.com/](http://www.dilicms.com/ "http://www.dilicms.com/") 打开的默认首页为:

[![image](http://www.smallerpig.com/wp-content/uploads/2015/04/image_thumb12.png "image")](http://www.smallerpig.com/wp-content/uploads/2015/04/image13.png)

浏览器中输入[http://localhost/install/public/](http://localhost/install/public/ "http://localhost/install/public/")可进入安装引导页:

[![image](http://www.smallerpig.com/wp-content/uploads/2015/04/image_thumb13.png "image")](http://www.smallerpig.com/wp-content/uploads/2015/04/image14.png)

点击同意协议并下一步之后进入平台选择!这里因为在本地环境,只能选择普通环境了.

[![image](http://www.smallerpig.com/wp-content/uploads/2015/04/image_thumb14.png "image")](http://www.smallerpig.com/wp-content/uploads/2015/04/image15.png)

再点击下一步进入环境检测,开始检测当前环境是否能够安装DiliCMS

[![image](http://www.smallerpig.com/wp-content/uploads/2015/04/image_thumb15.png "image")](http://www.smallerpig.com/wp-content/uploads/2015/04/image16.png)

这里我们所有条件都满足,下一步进入数据库链接设置界面:

[![image](http://www.smallerpig.com/wp-content/uploads/2015/04/image_thumb16.png "image")](http://www.smallerpig.com/wp-content/uploads/2015/04/image17.png)

这里还是能够发现很多BootStrap样式的影子.我事先在mysql中创建好了dilicms,所以这边直接点击导入数据库了.点击之后会弹出控制台,稍等一两秒之后会提示导入成功

[![image](http://www.smallerpig.com/wp-content/uploads/2015/04/image_thumb17.png "image")](http://www.smallerpig.com/wp-content/uploads/2015/04/image18.png)

再点击下一步之后设置超级管理员账户和密码.

[![image](http://www.smallerpig.com/wp-content/uploads/2015/04/image_thumb18.png "image")](http://www.smallerpig.com/wp-content/uploads/2015/04/image19.png)[![image](http://www.smallerpig.com/wp-content/uploads/2015/04/image_thumb19.png "image")](http://www.smallerpig.com/wp-content/uploads/2015/04/image20.png)

创建成功之后我们的安装也就完成了.

然后输入在浏览器里输入localhost/admin即可进入后台登陆页面.

[![image](http://www.smallerpig.com/wp-content/uploads/2015/04/image_thumb20.png "image")](http://www.smallerpig.com/wp-content/uploads/2015/04/image21.png)

来看看后台的默认首页:

[![image](http://www.smallerpig.com/wp-content/uploads/2015/04/image_thumb21.png "image")](http://www.smallerpig.com/wp-content/uploads/2015/04/image22.png)

还是挺熟悉的那种赶脚.接下来小猪也介绍的也就是整个CMS的重点了,那就是动态的添加数据模型:假如我们要做的是一个新闻发布系统.新闻有标题,简介,分类,内容这几个字段.其中分类字段引用了另外一张表 也就是分类模型.

所以我们首先要新建一个分类模型,取名为:article_category:

[![image](http://www.smallerpig.com/wp-content/uploads/2015/04/image_thumb22.png "image")](http://www.smallerpig.com/wp-content/uploads/2015/04/image23.png)[![image](http://www.smallerpig.com/wp-content/uploads/2015/04/image_thumb23.png "image")](http://www.smallerpig.com/wp-content/uploads/2015/04/image24.png)

到列表也给该分类模型添加字段:

[![image](http://www.smallerpig.com/wp-content/uploads/2015/04/image_thumb24.png "image")](http://www.smallerpig.com/wp-content/uploads/2015/04/image25.png)

[![image](http://www.smallerpig.com/wp-content/uploads/2015/04/image_thumb25.png "image")](http://www.smallerpig.com/wp-content/uploads/2015/04/image26.png)

[![image](http://www.smallerpig.com/wp-content/uploads/2015/04/image_thumb26.png "image")](http://www.smallerpig.com/wp-content/uploads/2015/04/image27.png)

添加完成后相同步骤我们进入”内容模型管理”板块添加模型数据,article.

[![image](http://www.smallerpig.com/wp-content/uploads/2015/04/image_thumb27.png "image")](http://www.smallerpig.com/wp-content/uploads/2015/04/image28.png)

其字段列表为:

[![image](http://www.smallerpig.com/wp-content/uploads/2015/04/image_thumb28.png "image")](http://www.smallerpig.com/wp-content/uploads/2015/04/image29.png)

其中我们单独介绍文章分类字段.

[![image](http://www.smallerpig.com/wp-content/uploads/2015/04/image_thumb29.png "image")](http://www.smallerpig.com/wp-content/uploads/2015/04/image30.png)

可以看到小猪将数据源的值设置成了:article_category|category_name.这个值对应我们刚刚新建的分类模型的”模型名|字段名”!,具体的各字段类型和数据源参数的设置可以参考作者在社区发表的帖子:[http://codeigniter.org.cn/forums/forum.php?mod=viewthread&amp;tid=9699&amp;extra=page%3D1](http://codeigniter.org.cn/forums/forum.php?mod=viewthread&amp;tid=9699&amp;extra=page%3D1 "http://codeigniter.org.cn/forums/forum.php?mod=viewthread&amp;tid=9699&amp;extra=page%3D1")

然后我们给模型字段添加几个值:分别为文章的几个类别:体育,娱乐,军事!

[![image](http://www.smallerpig.com/wp-content/uploads/2015/04/image_thumb30.png "image")](http://www.smallerpig.com/wp-content/uploads/2015/04/image31.png)

回到内容管理的文章页,然后点击添加就可以看到该页面为我们刚才设置的参数值:

[![image](http://www.smallerpig.com/wp-content/uploads/2015/04/image_thumb31.png "image")](http://www.smallerpig.com/wp-content/uploads/2015/04/image32.png)

基本功能我们已经体验了,

这样,我们将系统发布了之后维护人员根本不需要懂太多的代码就能够管理整个系统,当然作为我们开发人员的工具也可以节省我们开发后台的很多时间.

另外,系统还给我们提供了权限的管理,数据库的备份还原优化,缓存的功能以及常规站点的设置.可以说基本上能满足我们功能!

小猪准备在其基础上进行二次开发包括数据字段为上传图片地址的功能.?默认的编辑器是使用的KindEditor将其换成ckeditor.等等.这都得益于其是基于CI框架!