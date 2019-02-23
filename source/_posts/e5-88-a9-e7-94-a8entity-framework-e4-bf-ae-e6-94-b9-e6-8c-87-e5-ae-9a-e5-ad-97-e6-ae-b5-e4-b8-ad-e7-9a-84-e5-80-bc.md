---
title: 利用Entity Framework修改指定字段中的值
tags:
  - entity framwork
  - SQL SERVER
id: 962
categories:
  - ASP.NET
  - SQL
date: 2015-12-03 14:30:08
---

### 问题

一般我们编辑某些模型的时候会用到类似这样的代码：

    [HttpPost]
    public ActionResult Edit(Article model)
    {
        if (model.Id == 0)
        {
            return HttpNotFound\(\);
        }
        using (db)
        {
            db.Entry(model).State = EntityState.Modified;
            db.SaveChanges\(\);
        }
        return RedirectToAction("Index");
    }


 ```

    这样的代码完全能够符合我们的要求，能够顺利的修改我们想要修改的数据。
    但是其中却有一个问题：例如说文章的创建时间字段`Create_Time`是由文章创建的时候生成的，其值不应该随着文章的编辑而变化，为了使这个值不变，我们就需要在前台的html代码里加上一个隐藏的name属性为Create_Time的input并且设置其值为文章的创建时间。这样做也能成功但是不保险，如果编辑人员有能力修改input里的值的话还是能够修改数据库中的数据的。所以我们应该从代码的层面上来解决这个问题。
    再举个例子，用户在修改密码的时候我们只希望单单的修改密码字段，而不需要将原来的用户模型全都加载进来，然后再修改密码，再保存。这样做感觉上是已经多走了好多的路。
    而如果我们直接手写sql语句的话我们可以直接执行SQL语句：

```  

 update userinfo set password = '123456'


 ```

    很显然如果我们类似使用dbContext的执行sql语句就有点本末倒置了：

```  

 db.Database.ExecuteSqlCommand("update userinfo set password = '123456'");


 ```

    ### 解决办法

    使用类似下列代码：也就是只设置模型的某些特定的字段来告诉EF我们希望修改哪些字段。

```  

 public ActionResult TestEdit(int id=0)
    {
        var model = new Article {Id = id, Title = $"after edit!current time: {DateTime.Now}"};
        db.Article.Attach(model);
        db.Entry(model).Property(x =&gt; x.Title).IsModified = true;
        db.SaveChanges\(\);
        return RedirectToAction("Index");
    }

上述代码只修改了文章的标题，而其他字段并没有做任何的修改，整个修改过程也没涉及到任何的读取数据库操作。这样的修改显然要更加合理。