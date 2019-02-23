---
title: 编写自己的Windows Live Writer插件
id: 420
categories:
  - ASP.NET
  - 'C#'
  - MVC
  - WEBFORM
  - WEB开发
date: 2014-02-10 12:34:19
tags:
---

## 起因

自从小猪使用Windows Live Writer(wlw)来写博客之后就很少打开网站的后台编辑器了，这真是个写博客的好东西啊，但是任何东西都是不完美的。索契冬奥会开幕式都会把五环弄成四环呢！对于写博一个很简单的情景：小猪需要从别的网站上面引用一段话，我希望引用的这端话有独立的标签好定义其样式，就这么个简单的需求在原生的wlw里面却束手无策了。好在现在的软件都强调对其自身的拓展，wlw也不例外。

## 准备

其实需求很简单，就是希望能有那么个按钮，这个按钮的功能是给我的文字加上一段固定的标签就可以了。参考很多的代码高亮插件之后发现这个完全是可行的，而且相比于代码高亮来说实在是再简单不过了——我不需要针对每个关键字设置不同的标签样式，也就是我不需要解析插入的内容直接贴上去就好了。

有了上述思想准备后小猪就开始了自己的插件开发

### 第一步：创建类库项目

使用VS来创建类库项目：需要注意的是.NET的版本一定要是3.0以下(包括)的。小猪做的时候就在这上面浪费了很长的时间。

### 第二步：对官方API库的引用

库在哪里？不需要去下载，只要电脑上安装了wlw，这个库就静静的躺在你的电脑里wlw的安装文件目录下，例如：C:Program Files (x86)Windows LiveWriterSystem.Windows.Forms.dll。当然因为需要新建一个输入代码的窗口，所以需要对System.Windows.Forms库的引用。

### 第三步：设置生成路径

因为我们很多时候需要实时预览我们的插件，避免每次编译好了都自己安装的麻烦我们设置生成路径，让VS编译的时候直接帮我们生成的DLL文件放到指定的文件夹下。

打开项目属性设置生成事件，在“后期生成事件命令行:”一栏中输入:XCOPY /D /Y /R "$(TargetPath)" "C:Program Files (x86)Windows LiveWriterPlugins"。保存并退出！

### 第四步：编写自己的类实现ContentSource类的方法

```
[WriterPlugin("72371eef-e350-4aae-af28-91613a9137e3", "smallerpig",
    PublisherUrl = "http://www.smallerpig.com", 
    Description = "This is an Test plugin.", Name = "smallerpigPlugin")]
[InsertableContentSource("smallerpigPlugin", SidebarText = "smallerpigPlugin")]
public class smallerpigPlugin : ContentSource
{
    public override DialogResult CreateContent(IWin32Window dialogOwner, ref string content)
    {
        new AddFrom\(\).ShowDialog\(\);
        content = ContentProcessor.ProcessedContent;
        return (!String.IsNullOrEmpty(content) ? DialogResult.OK : DialogResult.No);
    }
}
```


1.  第一个参数为一个Guid，我们可以用Visual Studio自动生成，其标识的作用。
2.  第二个参数为该插件的名称，将在Windows Live Writer的插件管理器中看到。
3.  PublisherUrl为发布者的网站地址，这里就写了我的Blog地址。
4.  ImagePath为插件的图标文件路径，注意该图标应为18*16大小，且一定以Embedded Resource形式编译在程序集中。
5.  Description为插件的一小段描述介绍。

### 第五步：新建窗口

因为需要在点击插件按钮时弹出对话框让用户输入对应内容，所以新建一个Form窗体拖上两个控件写上事件
```
public partial class AddFrom : Form
{
    public AddFrom\(\)
    {
        InitializeComponent\(\);
    }

    /// <summary>
    /// End the process.
    /// </summary>
    private void EndProcess\(\)
    {
        this.Close\(\);
    }

    private void Btn_Ok_Click(object sender, EventArgs e)
    {
        ContentProcessor.Process(this.Txt_From.Text, this.contentTextBox.Text);

        this.EndProcess\(\);
    }

    private void Btn_Cancle_Click(object sender, EventArgs e)
    {
        this.EndProcess\(\);
    }

    private void contentTextBox_TextChanged(object sender, EventArgs e)
    {
    }
}
```

再新建一个类来单独处理加标签的逻辑

```
ublic class ContentProcessor
{
    /// <summary>
    /// The processed content.
    /// </summary>
    public static string ProcessedContent { get; private set; }

    /// <summary>
    /// The process function.
    /// </summary>
    /// <param name="originalContent">Original content which needs to be processed.</param>
    public static void Process(string originalFrom,string originalContent)
    {
        ProcessedContent = (!String.IsNullOrEmpty(originalContent)
            ? String.Format("<fieldset><legend><a href="{0}">引用:</a></legend><div style="padding:5px 10px;color:#666666;text-align: justify;">{1}</div></fieldset>", originalFrom, originalContent)
            : String.Empty);
    }
}
```

这个时候这个简单控件的完整代码就已经完成。编译后重新打开wlw就可以在插件栏里发现它。

参考：[http://www.cnblogs.com/dflying/archive/2006/12/03/580602.html](http://www.cnblogs.com/dflying/archive/2006/12/03/580602.html "http://www.cnblogs.com/dflying/archive/2006/12/03/580602.html")

> [http://www.cnblogs.com/yjf512/archive/2012/05/13/2498400.html](http://www.cnblogs.com/yjf512/archive/2012/05/13/2498400.html "http://www.cnblogs.com/yjf512/archive/2012/05/13/2498400.html")

## 后记

小猪写完这个简单的插件之后发现wlw不仅仅可以这么用，我们自己写的新闻发布页、博客等等程序其实都可以使用wlw来发布，如果需要一定自定义的格式或者要求我们完全可以使用插件来完成。这样我们就不需要在网页端使用富文本编辑器来编辑发布。有时间小猪好好研究研究者块内容。如果发现这个思路确实可行的话那么以后可以在项目中实施。