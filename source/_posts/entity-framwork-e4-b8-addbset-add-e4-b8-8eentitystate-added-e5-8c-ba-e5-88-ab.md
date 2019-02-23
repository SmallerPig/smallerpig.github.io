---
title: Entity Framwork中dbset.Add\(\)与EntityState.Added区别
tags:
  - entity framwork
id: 928
categories:
  - ASP.NET
  - MVC
date: 2015-11-03 20:53:23
---

通常我们在写ASP.NET MVC 程序在新加数据模型对象时通常会配合EF提供的方法。
例如在添加文章时我们都使用类似下面两种方法来将文章添加到数据库：

    //方法1
    DbContext.Entry(article).State = EntityState.Added;
    DbContext.SaveChanges\(\);

    //方法2
    DbContext.Article.Add(article);
    DbContext.SaveChanges\(\);


 ```

    可是上面两种方法有什么区别呢？
    仅仅从上面的代码来看是没有区别的，要想了解其中的区别我们得看看`article`d的源码，为了简单起见我们将模型写的简单一点。

```  

 public class Article
    {
        public int Id { get; set; }

        public User Author { get; set; }

        public string Title { get; set; }

        public string Content { get; set; }

        public int Status { get; set; }

    }

    public class User
    {
        public int Id { get; set; }

        public string Name { get; set; }
    }

上面模型中的文章包含作者字段，EF会自动在数据库中生成Author_Id字段，并且关联到User表中。
这个时候如果我们在新建文章的时候使用`DbContext.Article.Add(article);`而且article对象的Author属性不为空时，**那么EF总会在User表中新建一个对应的数据**。而使用`DbContext.Entry(article).State = EntityState.Added`方法时不会出现上述的问题。

引用老外的一句描述就是

> When you use `dbContext.SomeEntitySet.Add(entityInstance);` the status for this and all its related entities/collections is set to added, while `dbContext.Entry(entityInstance).State = EntityState.Added;` adds also all the related entities/collections to the context but leaves them as unmodified. So if the entity that you are trying to create has a related entity (and it's value its not null), when you use Add it will create a new object for that child entity, while with the other way it won't.