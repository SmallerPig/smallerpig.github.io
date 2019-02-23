---
title: Reverse query name for '*' clashes with field name '*'
tags:
  - Django
id: 939
categories:
  - Django
  - python
date: 2015-11-19 15:50:13
---

在已经新建的Article模型之后再新建Picture模型，模型中有外键指向Article:

    # models
    class Article(models.Model):
        title = models.CharField(max_length=50)
        create_time = models.DateTimeField(auto_now_add = True)
        summary = models.TextField(max_length=200)
        picture = models.ImageField(max_length=200)
        media = models.CharField(max_length=200)
        content = models.TextField\(\) # content of the article

        def __str__(self):
            return self.title

    class Picture(models.Model):
        title = models.CharField(max_length=200)
        create_time = models.DateTimeField(auto_now_add = True)
        update_time = models.DateTimeField(auto_now = True)
        article = models.ForeignKey(Article)

        def __str__(self):
            return self.title



 ```

    运行命令:python manage.py makemigrations 时报错：

    > SystemCehhckError: System check identified some issues:
> 
>       Errors:
>       bulls.Picture.article:(fields.E303) Reverse query name for 'picture.article' clashes with field name 'Article.picture'.
>       HINT: Rename field 'Article.picture', or add/change a related_name argument to the definition for field 'Picture.article'.

    猜测的原因是因为本身Article中已经有了picture字段，然后在新建的Picture中又定义了外键指向Article。这样会对在查询的时候产生歧义，例如类似代码article.picture就不知道是指本身的picture属性还是指附属在Article中的picture表中的数据，
    所以在定义picture时添加relate_name属性

```  

 article = models.ForeignKey(Article,related_name='append_article')
    