---
title: 'C#延迟加载'
tags:
  - 'C#'
id: 232
categories:
  - 'C#'
  - WEB开发
date: 2013-07-30 14:29:17
---

延迟加载(lazy loading) 设计模式是为了避免一些无谓的性能开销而提出来的，所谓延迟加载就是当在真正需要数据(读取属性值)的时候，才真正执行数据加载操作.

有效使用它可以大大提高系统性能.

<more></more>

为了便于理解, 我们来建立一个场景, 假设我们要构造一个Hero(英雄) 类, 每个Hero 有自己的名字和(SpecialSkill)特殊技能.

建模

这是一种建立的方法:
```


public class Hero
{
    public string FullName { get; set; }
    public string Name { get; set; }
    public SpecialSkill Skill{ get; set; }

    public Hero(string name)
    {
        Name = name;
        FullName = "Super " + name;
        Skill = new SpecialSkill(name);
    }
}
public class SpecialSkill
{
    public int Power { get; set; }
    public string SkillName { get; set; }
    public int StrengthSpent { get; set; }
    public SpecialSkill(string name)
    {
        Console.WriteLine("loading special skill .....");
        Power = name.Length;
        StrengthSpent = name.Length * 3;
        SkillName = name + " Blazing";
        Console.WriteLine(SkillName + ",... this's what makes a legend!");
    }
} 
class Program
{
    static void Main(string[] args)
    {
        Hero hero = new Hero("wukong");            
        Console.WriteLine("nn..............Press Enter to continue ................nn");
        Console.Read\(\);
        Console.WriteLine("Hero's special skill: " + hero.Skill.SkillName);
        Console.Read\(\);
        Console.Read\(\);
    }
}

 ```<div>运行程序后输出如下, 这个例子非常的容易理解, 结果也是显然的.</div>

![ScreenShot066](http://www.smallerpig.com/wp-content/plugins/wp-ueditor/ueditor/php/upload/89571375165521.jpg "ScreenShot066")

它的缺点是, 当运行Hero 构造函数的时候, SpecialSkill 的所有属性都已经加载了. 如果我们只想获取这个Hero 的FullName, 我们也加载了SpecialSkill 所有值. 

## 属性的加载延迟

在没有Lazy&lt;T&gt; 以前我们可以这样做:
```


public class Hero
 {
     public string FullName { get; set; }
     public string Name { get; set; }
     private SpecialSkill skill;
     public SpecialSkill Skill
     { 
         get { return skill ?? (skill = new SpecialSkill(Name)); }
     }
     public Hero(string name)
     {
         Name = name;
         FullName = "Super " + name;

     }
 }

 ```

即: 修改属性SpecialSkill的加载方法. 那么当我们再运行程序时, 得到的输出就是:

![ScreenShot067](http://www.smallerpig.com/wp-content/plugins/wp-ueditor/ueditor/php/upload/45811375165521.jpg "ScreenShot067")

非常好! 这就是我们要的效果, 这样可以让系统更加的有效率.

## Lazy&lt;T&gt;

当net framework 引入了Lazy&lt;T&gt; 类后, 我们也可以使用它来实现:
```


public class Hero
{
    public string FullName { get; set; }
    public string Name { get; set; }

    private readonly Lazy&lt;SpecialSkill&gt; skill;
    public SpecialSkill Skill
    {
        get { return skill.Value; }
    }

    public Hero(string name)
    {
        Name = name;
        FullName = "Super " + name;

        skill = new Lazy&lt;SpecialSkill&gt;(\(\) =&gt; new SpecialSkill(name));
    }
}

 ```

Lazy&lt;T&gt;提供对延迟初始化的支持。而 Lazy&lt;T&gt; 中的一个属性 Value, 则是获取当前 Lazy&lt;T&gt; 实例的延迟初始化值。

## Lazy&lt;T&gt;的优势

那么既然我们已经可以用属性缓存的方法实现, 为什么还要引入Lazy&lt;T&gt; ?

至少Lazy&lt;T&gt; 有以下几点优势:

1.  它具有 LazyThreadSafetyMode, 但是我们一般不使用它, 除非是很关键的情况下(在此略去181个字)

2.  它使属性的定义行更加简单

3.  从语义上来讲, 它更加明确, 更加具有可读性

4.  它允许null为有效值

## ASP.NET

其实小猪今天想到这个主题的原因是想到了一个面试题。在web页面中怎么实现数据的缓存，或者不是每次打开页面的时候都去访问数据库。

下篇小猪将做种说说对数据库的延迟加载或者缓存。