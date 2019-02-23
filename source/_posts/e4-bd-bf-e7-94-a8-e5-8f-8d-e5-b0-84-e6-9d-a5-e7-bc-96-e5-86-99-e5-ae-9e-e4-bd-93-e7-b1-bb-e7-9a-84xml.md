---
title: 使用反射来编写实体类的XML
id: 379
categories:
  - ASP.NET
  - 'C#'
  - MVC
  - WEBFORM
date: 2013-12-31 09:19:42
tags:
---

## 前言：

开发过程中经常需要返回某实体类的列表，公司通常用的都是XML格式的接口，小猪借鉴了公司前辈留下的代码一直是类似这么写的：
``` 

 public static string GetXMLList(IList&lt;Article&gt; articlelist)
{
using (MemoryStream memoryStream = new MemoryStream\(\))
{
    XmlWriterSettings xmlWriterSettings = new XmlWriterSettings\(\);
    xmlWriterSettings.Indent = true;
    xmlWriterSettings.Encoding = new UTF8Encoding(false);
    xmlWriterSettings.NewLineChars = Environment.NewLine;
    using (XmlWriter xmlWriter = XmlWriter.Create(memoryStream, xmlWriterSettings))
    {
        xmlWriter.WriteStartDocument(true);
        xmlWriter.WriteStartElement("Articles");
        if (articlelist != null)
        {
            foreach (var article in articlelist)
            {
                xmlWriter.WriteStartElement("Article");
                xmlWriter.WriteStartAttribute("id");
                xmlWriter.WriteString(article.Id.ToString\(\));
                xmlWriter.WriteEndAttribute\(\);

                xmlWriter.WriteStartElement("Title");
                xmlWriter.WriteCData(article.Title);
                xmlWriter.WriteEndElement\(\);

                xmlWriter.WriteStartElement("Summary");
                xmlWriter.WriteCData(article.Summary);
                xmlWriter.WriteEndElement\(\);

                xmlWriter.WriteStartElement("Author");
                xmlWriter.WriteCData(article.Author);
                xmlWriter.WriteEndElement\(\);

                xmlWriter.WriteStartElement("CreateDate");
                xmlWriter.WriteCData(article.CreateDate.ToString("yyyy-MM-dd"));
                xmlWriter.WriteEndElement\(\);

                xmlWriter.WriteStartElement("BannerURL");
                xmlWriter.WriteCData(article.BannerURL);
                xmlWriter.WriteEndElement\(\);

                xmlWriter.WriteStartElement("ImageURL");
                xmlWriter.WriteCData(article.ImageURL);
                xmlWriter.WriteEndElement\(\);

                xmlWriter.WriteStartElement("ImageAuthor");
                xmlWriter.WriteCData(article.ImageAuthor);
                xmlWriter.WriteEndElement\(\);

                xmlWriter.WriteStartElement("Category");
                xmlWriter.WriteCData(article.Category.ToString\(\));
                xmlWriter.WriteEndElement\(\);

                xmlWriter.WriteEndElement\(\);
            }
        }
        xmlWriter.WriteEndElement\(\);
        xmlWriter.WriteEndDocument\(\);
    }
    string xml = Encoding.UTF8.GetString(memoryStream.ToArray\(\));
    return xml;
｝
}

 ```
生成的代码：
``` 

 &lt;?xml version="1.0" encoding="utf-8" standalone="yes"?&gt;
&lt;Articles&gt;
  &lt;Article id="83"&gt;
    &lt;Title&gt;&lt;![CDATA[存储]]&gt;&lt;/Title&gt;
    &lt;Summary&gt;&lt;![CDATA[asdsa]]&gt;&lt;/Summary&gt;
    &lt;Author&gt;&lt;![CDATA[asdsa]]&gt;&lt;/Author&gt;
    &lt;CreateDate&gt;&lt;![CDATA[2013-12-30]]&gt;&lt;/CreateDate&gt;
    &lt;BannerURL&gt;&lt;![CDATA[/Images/2013年12月/20131230153738573.png]]&gt;&lt;/BannerURL&gt;
    &lt;ImageURL&gt;&lt;![CDATA[/Images/2013年12月/201312301<span id="transmark"></span>53731941.png]]&gt;&lt;/ImageURL&gt;
    &lt;ImageAuthor&gt;&lt;![CDATA[sda]]&gt;&lt;/ImageAuthor&gt;
    &lt;Category&gt;&lt;![CDATA[1]]&gt;&lt;/Category&gt;
  &lt;/Article&gt;
  &lt;Article id="81"&gt;&lt;span id="transmark"&gt;&lt;/span&gt;
    &lt;Title&gt;&lt;![CDATA[存储]]&gt;&lt;/Title&gt;
    &lt;Summary&gt;&lt;![CDATA[asdsa]]&gt;&lt;/Summary&gt;
    &lt;Author&gt;&lt;![CDATA[asdsa]]&gt;&lt;/Author&gt;
    &lt;CreateDate&gt;&lt;![CDATA[2013-12-30]]&gt;&lt;/CreateDate&gt;
    &lt;BannerURL&gt;&lt;![CDATA[/Images/2013年12月/20131230153738573.png]]&gt;&lt;/BannerURL&gt;
    &lt;ImageURL&gt;&lt;![CDATA[/Images/2013年12月/20131230153731941.png]]&gt;&lt;/ImageURL&gt;
    &lt;ImageAuthor&gt;&lt;![CDATA[sda]]&gt;&lt;/ImageAuthor&gt;
    &lt;Category&gt;&lt;![CDATA[1]]&gt;&lt;/Category&gt;
  &lt;/Article&gt;
&lt;/Articles&gt;

 ```
<span style="font-size: 13px; line-height: 1.6em;">代码一直延续到昨天！小猪决定重构他！</span>

## 重构一：去除重复代码

首先上述代码最大的问题就是大量的复制粘贴，违背了DRY(Don't Repeated Yourself)原则。多个字段就要多粘贴一次，为了解决这个问题在遍历实体列表时使用下属代码：
``` 

 using (MemoryStream memoryStream = new MemoryStream\(\))
{
    XmlWriterSettings xmlWriterSettings = new XmlWriterSettings\(\);
    xmlWriterSettings.Indent = true;
    xmlWriterSettings.Encoding = new UTF8Encoding(false);
    xmlWriterSettings.NewLineChars = Environment.NewLine;
    using (XmlWriter xmlWriter = XmlWriter.Create(memoryStream, xmlWriterSettings))
    {
        xmlWriter.WriteStartDocument(true);
        xmlWriter.WriteStartElement("Articles");
        if (articlelist != null)
        {
            foreach (var article in articlelist)
            {
                Type type = typeof(Article);
                foreach (PropertyInfo propertyInfo in type.GetProperties\(\))
                {
                    object ob = propertyInfo.GetValue(article, null);
                    if (null != ob)
                    {
                        xmlWriter.WriteStartElement(propertyInfo.Name);
                        xmlWriter.WriteCData(ob.ToString\(\));
                        xmlWriter.WriteEndElement\(\);
                    }
                }
                xmlWriter.WriteEndElement\(\);
            }
        }
        xmlWriter.WriteEndElement\(\);
        xmlWriter.WriteEndDocument\(\);
    }

    string xml = Encoding.UTF8.GetString(memoryStream.ToArray\(\));
    return xml;
}

 ```
<span style="font-size: 13px; line-height: 1.6em;">可是这样只能遍历Article类型，其他类型还是使用不了这个方法</span>

## 重构二：加入泛型

在最原始的代码中每为一个实体增加类似功能的时候都要把那一整块代码复制过来然后做修改，我们在重构一中还是没有解决这个问题，为了使上面的方法能够在以后被重复利用我们加入泛型
``` 

 public static string GetXMLList&lt;T&gt;(IList&lt;T&gt; articlelist)
{
    using (MemoryStream memoryStream = new MemoryStream\(\))
    {
        XmlWriterSettings xmlWriterSettings = new XmlWriterSettings\(\);
        xmlWriterSettings.Indent = true;
        xmlWriterSettings.Encoding = new UTF8Encoding(false);
        xmlWriterSettings.NewLineChars = Environment.NewLine;
        using (XmlWriter xmlWriter = XmlWriter.Create(memoryStream, xmlWriterSettings))
        {
            xmlWriter.WriteStartDocument(true);
            xmlWriter.WriteStartElement("Roots");
            Type type = typeof(T);
            if (articlelist != null)
            {
                foreach (var article in articlelist)
                {
                    foreach (PropertyInfo propertyInfo in type.GetProperties\(\))
                    {
                        object ob = propertyInfo.GetValue(article, null);
                        if (null != ob)
                        {
                            xmlWriter.WriteStartElement(propertyInfo.Name);
                            xmlWriter.WriteCData(ob.ToString\(\));
                            xmlWriter.WriteEndElement\(\);
                        }
                    }
                    xmlWriter.WriteEndElement\(\);
                }
            }
            xmlWriter.WriteEndElement\(\);
            xmlWriter.WriteEndDocument\(\);
        }

        string xml = Encoding.UTF8.GetString(memoryStream.ToArray\(\));
        return xml;
    }
}

 ```
这样代码完成了多类型的使用，但是却把所有的共有属性都写进了XML,在实际使用中我们可能不希望把所有属性都列进来，例如是否推荐字段，阅读权限字段等等~

重构三：

&nbsp;

&nbsp;

## 重构:...

...

## 重构N

为了使每个节点上面增加Id属性，定义一个抽象类`Listable`。抽象类中包涵自动Id，让需要提供列表的实体类继承至这个抽象类：
``` 

 /*==========================================================
*作者：SmallerPig
*时间：2013/12/30 17:26:01
*版权所有:无锡睿阅数字科技有限公司
============================================================*/
namespace RY.Entity
{
    public abstract class Listable
    {
        public int Id { get; set; }
    }
}

 ```
实体类来继承它。例如：
``` 

 public class Article : Listable
{
    public string Title { get; set; }

    public string Summary { get; set; }

｝

 ```
然后给泛型方法加上约束，在该指定Id的地方加上id属性！
``` 

 static string ToXML&lt;T&gt;(IList&lt;T&gt; TList, string ingor) where T : Listable
{
    using (MemoryStream memoryStream = new MemoryStream\(\))
    {
        XmlWriterSettings xmlWriterSettings = new XmlWriterSettings\(\);
        xmlWriterSettings.Indent = true;
        xmlWriterSettings.Encoding = new UTF8Encoding(false);
        xmlWriterSettings.NewLineChars = Environment.NewLine;
        using (XmlWriter xmlWriter = XmlWriter.Create(memoryStream, xmlWriterSettings))
        {
            xmlWriter.WriteStartDocument(true);
            xmlWriter.WriteStartElement("Roots");
            if (TList != null)
            {
​                Type type = typeof(T);
                foreach (T t in TList)
                {
                    xmlWriter.WriteStartElement(type.Name);
                    xmlWriter.WriteStartAttribute("id");
                    xmlWriter.WriteString(t.Id.ToString\(\));
                    xmlWriter.WriteEndAttribute\(\);                            
                    foreach (PropertyInfo propertyInfo in type.GetProperties\(\))
                    {
                        if (propertyInfo.CanRead &amp;&amp; propertyInfo.Name.ToLower\(\) != ingor.ToLower\(\))
                        {
                            if (propertyInfo.PropertyType == typeof(DateTime))
                            {
                                xmlWriter.WriteStartElement(propertyInfo.Name);
                                DateTime dt = Convert.ToDateTime(propertyInfo.GetValue(t, null));
                                xmlWriter.WriteCData(dt.ToString("yyyy-MM-dd hh:mm:ss"));
                                xmlWriter.WriteEndElement\(\);
                            }
                            if (propertyInfo.PropertyType == typeof(String) || propertyInfo.PropertyType == typeof(int))
                            {
                                object ob = propertyInfo.GetValue(t, null);
                                if (null != ob)
                                {
                                    xmlWriter.WriteStartElement(propertyInfo.Name);
                                    xmlWriter.WriteCData(ob.ToString\(\));
                                    xmlWriter.WriteEndElement\(\);
                                }
                            }
                        }
                    }
                    xmlWriter.WriteEndElement\(\);
                }
            }
            xmlWriter.WriteEndElement\(\);
            xmlWriter.WriteEndDocument\(\);
        }
        string xml = Encoding.UTF8.GetString(memoryStream.ToArray\(\));
        return xml;
    }
}

 ```
最后效果：
``` 

 &lt;?xml version="1.0" encoding="utf-8" standalone="yes"?&gt;
&lt;Roots&gt;
  &lt;Article id="83"&gt;
    &lt;Title&gt;&lt;![CDATA[存储]]&gt;&lt;/Title&gt;
    &lt;Summary&gt;&lt;![CDATA[asdsa]]&gt;&lt;/Summary&gt;
    &lt;Author&gt;&lt;![CDATA[asdsa]]&gt;&lt;/Author&gt;
    &lt;Category&gt;&lt;![CDATA[1]]&gt;&lt;/Category&gt;
    &lt;CreateDate&gt;&lt;![CDATA[2013-12-30 03:37:52]]&gt;&lt;/CreateDate&gt;
    &lt;BannerURL&gt;&lt;![CDATA[/Images/2013年12月/20131230153738573.png]]&gt;&lt;/BannerURL&gt;
    &lt;ImageURL&gt;&lt;![CDATA[/Images/2013年12月/20131230153731941.png]]&gt;&lt;/ImageURL&gt;
    &lt;ImageAuthor&gt;&lt;![CDATA[sda]]&gt;&lt;/ImageAuthor&gt;
    &lt;Id&gt;&lt;![CDATA[83]]&gt;&lt;/Id&gt;
  &lt;/Article&gt;
  &lt;Article id="82"&gt;
    &lt;Title&gt;&lt;![CDATA[存储]]&gt;&lt;/Title&gt;
    &lt;Summary&gt;&lt;![CDATA[asdsa]]&gt;&lt;/Summary&gt;
    &lt;Author&gt;&lt;![CDATA[asdsa]]&gt;&lt;/Author&gt;
    &lt;Category&gt;&lt;![CDATA[1]]&gt;&lt;/Category&gt;
    &lt;CreateDate&gt;&lt;![CDATA[2013-12-30 03:37:51]]&gt;&lt;/CreateDate&gt;
    &lt;BannerURL&gt;&lt;![CDATA[/Images/2013年12月/20131230153738573.png]]&gt;&lt;/BannerURL&gt;
    &lt;ImageURL&gt;&lt;![CDATA[/Images/2013年12月/20131230153731941.png]]&gt;&lt;/ImageURL&gt;
    &lt;ImageAuthor&gt;&lt;![CDATA[sda]]&gt;&lt;/ImageAuthor&gt;
    &lt;Id&gt;&lt;![CDATA[82]]&gt;&lt;/Id&gt;
  &lt;/Article&gt;
&lt;/Roots&gt;&lt;span id="transmark"&gt;&lt;/span&gt;

 ```
&nbsp;