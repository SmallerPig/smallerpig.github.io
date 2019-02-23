---
title: 使用EntityFrameworkWithHierarchyId
tags:
  - entity framwork
id: 972
categories:
  - MVC
  - SQL
date: 2016-01-27 18:13:51
---

SQL SERVER2008以后提供了[HierarchyId的数据类型](https://msdn.microsoft.com/zh-cn/ch/library/bb677290.aspx).利用该类型我们可以方便的使用'/'(默认的)来构建树形结构的数据,典型的结构包括文章类型(category),人员关系(emplloy),评论盖楼(comment)等.

SQL SERVER提供了该数据类型,EF自然也需要提供对应的数据类型来支持该数据库类型与C#类型的映射.很可惜默认的EF6是不支持该数据类型的.如果想使用EF的HierarchyId类型就需要使用`EntityFrameworkWithHierarchyId`替换原来的EntityFramework了.
安装方法"
可以使用Nugget管理器搜索安装
或者在程序包管理器里输入下列代码

    install-package EntityFrameworkWithHierarchyId


 ```

    当然直接下载dll然后手动引用也可以.

    安装后之后我们可以在命名空间`System.Data.Entity.Hierarchy`中找到HierarchyId的定义.
    我们就可以定义如下的模型来对应数据库的hierarchyid类型.

```  

 public class Category
    {
        public int Id { get; set; }

        public string Title { get; set; }

        public HierarchyId Path { get; set; }
    }


 ```

    然后我们就可以愉快的使用HierarchyId类啦.
    实例化一个HierarchyId类:

```  

 HierarchyId model = new HierarchyId("/2/6/8");


 ```

    在具体的dbcontext中使用:

```  

 [HttpPost]
    [ValidateAntiForgeryToken]
    public ActionResult Add(Category model)
    {
        model.CreateTime = DateTime.Now;
        model.Path = new HierarchyId(Request.Form["path"]);
        db.Categories.Add(model);
        db.SaveChanges\(\);
        return RedirectToAction("Index");
    }

*   GetLevel\(\)方法获取对象当前所在层级
*   GetAncestor\(\)取得某一个级别的祖先
*   GetDescendant\(\)取得某一个级别的子代
*   IsDescendantOf\(\)判断某个节点是否为某个节点的子代
*   GetRoot\(\)取得根
*   GetReparentedValue\(\)可以用来移动节点（或者子树）
在linq查询中该类可以直接转换成对应的sql语句.