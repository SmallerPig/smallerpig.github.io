---
title: Entity Framework 筛选ICollection类型的属性
tags:
  - entity framwork
id: 982
categories:
  - ASP.NET
  - 'C#'
date: 2016-03-23 17:16:37
---

小猪尝试解决了这个问题两三天了，具体问题就是数据库中有活动表(Activity)，用户表(User)，用户可以报名参加活动，这样就产生了多对多的报名表(Enroll)。
```
public class Activity{
    public int Id {get;set;}
    public string Title {get;set;}
    public string Banner {get;set;}
    public virtual ICollection&lt;User&gt; Enrolls {get;set;}
}

public class User{
    public int Id {get;set;}
    public string UserName {get;set;}
}

public class UserEnrollActivity{
        public int UserId { get; set; }
        public virtual User User { get; set; }
        public int ActivityId { get; set; }
        public virtual Article Activity { get; set; }
        public int Value {get;set;}
}



```

在编写API时候需要提供这么一个接口，该接口可接受UserId的参数，来标识当前用户，在用户未登陆的情况下UserId的值为0。接口返回活动的基本信息，并且需要附加上传递过来的UserId所对应的用户的报名信息。

就是在编写这个接口过程中发现问题。
一开始的代码类似：

```  

return
    DbContext.Activity.Take(count)
        .Select(item=&gt; new ActivityListViewModel\(\)
        {
            Id=item.Id,
            Banner = item.Banner,
            Title = item.Title,
            Value = (item.Enrolls.FirstOrDefault(e =&gt; e.ActivityId == item.Id &amp;&amp; e.UserId == userId) ?? new UserEnrollActivity { Value = -1}).Value
        });


```

发现报错信息为：`The entity or complex type 'UserEnrollActivity' cannot be constructed in a LINQ to Entities query.`。大概意思为不能在Linq To Entities查询中实例化复杂类型 UserEnrollActivity>。Google一通之后发现有人提出了在select调用之前先调用ToList\(\)方法。

```  

return
    DbContext.Activity.Take(count).ToList\(\)
        .Select(item=&gt; new ActivityListViewModel\(\)
        {
            Id=item.Id,
            Banner = item.Banner,
            Title = item.Title,
            Value = (item.Enrolls.FirstOrDefault(e =&gt; e.ActivityId == item.Id &amp;&amp; e.UserId == userId) ?? new UserEnrollActivity { Value = -1}).Value
        });


```

尝试了之后代码确实能够正常运行了。
但是这个代码却有着很严重的性能问题，也就是其实该方法将活动的所有报名信息都加载到了内存中，如果某一活动有成千上万甚至上亿人报名的话那这些数据同样都被加载到内存中之后才开始筛选当前用户的报名信息。
意识到这个性能问题之后小猪一直在想怎么优化这段代码。在重复尝试之后终于查找到了[类似这样的提问](http://stackoverflow.com/questions/7753039/entity-framework-querying-child-entities).经过提示，小猪最终优化了代码

```  

return
    DbContext.Activity.Take(count).ToList\(\)
        .Select(item=&gt; new
        {
            Id=item.Id,
            Banner = item.Banner,
            Title = item.Title,
            Enroll = item.Enrolls.FirstOrDefault(e =&gt; e.UserId == userId)
        }).ToList\(\)
        .Select(item=&gt; new ActivityListViewModel\(\)
        {
            Id=item.Id,
            Banner = item.Banner,
            Title = item.Title,
            Value = (item.Enroll ?? new UserEnrollActivity {Value = -1}).Value
        }
    );
```
上述代码中使用了两次select方法，第一次select只将当前用户的报名信息保存到一个临时的匿名对象中，然后对这个列表进行ToList\(\)操作，这样其实只将一条报名信息保存到内存，然后对数据进行筛选。
这样既避免了复杂类型无法实例化的问题，也解决了性能的问题。