---
title: Django学习笔记
tags:
  - Django
id: 912
categories:
  - Django
  - WEB开发
date: 2015-10-30 10:37:40
---

#### 查看安装版本:

    import django
    print(django.get_version\(\)) # 1.8.2

 ```

    #### 创建一个项目:

   ```
   django-admin startproject mysite

 ```

    可以生成如下结构的文件夹：

   ```
   mysite/
        manage.py
        mysite/
            __init__.py
            settings.py
            urls.py
            wsgi.py

 ```

    默认的，下列app 会随着django的安装而默认的被使用：
    By default, INSTALLED_APPS contains the following apps, all of which come with Django:
    django.contrib.admin – The admin site. 
    django.contrib.auth – An authentication system.
    django.contrib.contenttypes – A framework for content types.
    django.contrib.sessions – A session framework.
    django.contrib.messages – A messaging framework.
    django.contrib.staticfiles – A framework for managing static files.

    #### 初始化数据库:

   ```
   python manage.py migrate

 ```

    #### 启动测试服务器:

   ```
   python manage.py runserver 8080

 ```

    如果不加后面的8080端口号的话则默认使用8000，如果8000没有被其他程序占用的话。

    ### app部分

    #### 创建一个app:

   ```
   python manage.py startapp polls

 ```

    这段代码会创建如下格式的文件夹结果

   ```
   polls/
        __init__.py
        admin.py
        migrations/
            __init__.py
        models.py
        tests.py
        views.py

 ```

    #### 创建模型:

   ```
   from django.db import models

    class Question(models.Model):
        question_text = models.CharField(max_length=200)
        pub_date = models.DateTimeField('date published')

    class Choice(models.Model):
        question = models.ForeignKey(Question)
        choice_text = models.CharField(max_length=200)
        votes = models.IntegerField(default=0)

 ```

    模型都继承自类`models.Model`,对应的一个属性就对应一个数据库字段。以`Quesiton`为例，其有两个字段`question_text`和`pub_date`，分别为`Char`类型(长度为200)和`DateTime`类型。
    最大长度的问题：[max_length](https://docs.djangoproject.com/en/1.8/ref/models/fields/#django.db.models.CharField.max_length),默认值的问题:[default](https://docs.djangoproject.com/en/1.8/ref/models/fields/#django.db.models.Field.default),外键的问题:[models.ForeignKey](https://docs.djangoproject.com/en/1.8/ref/models/fields/#django.db.models.ForeignKey)!
    值得注意的是上述类型中我们并没有为每一个类定义id字段。
    然后将新建的app加入到项目中。`INSTALLED_APPS=( ...,'polls')`
    然后再使用迁移的方法新建数据库

   ```
   python manage.py makemigrations polls

 ```

    上述模型代码会为我们生成如下的sql语句,通过命令`python manage.py sqlmigrate polls 0001`我们可以参考到

   ```
   BEGIN;
    CREATE TABLE "polls_choice" (
        "id" serial NOT NULL PRIMARY KEY,
        "choice_text" varchar(200) NOT NULL,
        "votes" integer NOT NULL
    );
    CREATE TABLE "polls_question" (
        "id" serial NOT NULL PRIMARY KEY,
        "question_text" varchar(200) NOT NULL,
        "pub_date" timestamp with time zone NOT NULL
    );
    ALTER TABLE "polls_choice" ADD COLUMN "question_id" integer NOT NULL;
    ALTER TABLE "polls_choice" ALTER COLUMN "question_id" DROP DEFAULT;
    CREATE INDEX "polls_choice_7aa0f6ee" ON "polls_choice" ("question_id");
    ALTER TABLE "polls_choice"
      ADD CONSTRAINT "polls_choice_question_id_246c99a640fbbd72_fk_polls_question_id"
        FOREIGN KEY ("question_id")
        REFERENCES "polls_question" ("id")
        DEFERRABLE INITIALLY DEFERRED;

    COMMIT;

 ```

*   可见上述sql语句自动为我们生成了主键id，当然我们也可以自己加上 ,
*   表名称为app名+类名(全小写)！
*   外键的名字默认为外键名+_id!
*   会根据使用的不同数据库来生成符合不同数据库规范的sql语句
*   `sqlmigrate`语句并没有执行，而仅仅是生成了sql.如果想要执行的话就需要运行命令:`python manage.py migrate`

    #### 本地使用model的api

    进入脚本模式`python manage.py shell`
    然后可执行一些语句来直接操作数据库了；

   ```
   >>> from polls.models import Question, Choice  
    >>> Question.objects.all\(\)
    []
    >>> from django.utils import timezone
    >>> q = Question(question_text="What's new?", pub_date=timezone.now\(\))
    >>> q.save\(\)
    >>> q.id
    1
    >>> q.question_text
    "What's new?"
    >>> q.pub_date
    datetime.datetime(2012, 2, 26, 13, 0, 0, 775217, tzinfo=<UTC>)
    # Change values by changing the attributes, then calling save\(\).
    >>> q.question_text = "What's up?"
    >>> q.save\(\)
    # objects.all\(\) displays all the questions in the database.
    >>> Question.objects.all\(\)
    [<Question: Question object>]

 ```

    ### 自带的Admin管理后台

    #### 创建一个超级管理员

   ```
   python manage.py createsuperuser
    Username:admin
    Email address:admin@example.com
    password:
    Password(again):
    Superuser created successfully.

 ```

    这样就可以在启动测试服务器之后在网址:hostname/admin中进入后台。
    如果过程出错的话确保admin模块配置正确和Url配置正确。系统已经默认生成了用户组合用户模块以及基本的权限控制。
    进入polls/admin.py让我们新建的app能够在后台中编辑内容。

   ```
   from django.contrib import admin
    from .models import Question

    admin.site.regitster(Question)

 ```

*   表单是系统根据我们的Question模型生成的。
*   模型的不同字段会生成不同的输入框类型。例如时间格式的会显示成日期选择器。
    后台还会记录用户操作的记录。

    #### 自定义表单

    将Question注册到admin的代码修改成：

   ```
   from django.contrib import admin
    from .models import Question

    class QuestionAdmin(admin.MOdelAdmin):
        fields = ['pub_date', 'question_text']

    admin.site.register(Question,QuestionAdmin)

 ```

    这样就能够使用QuesitonAdmin类来替代Question来注册到后台中。可以将QuestionAdmin定义区块来显示：

   ```
   class QuestionAdmin(admin.ModelAdmin):
        fieldsets = [
            (None,               {'fields': ['question_text']}),
            ('Date information', {'fields': ['pub_date']}),
        ]

 ```

    可以增加collapse来对某一组数据进行隐藏：

   ```
   class QuestionAdmin(admin.ModelAdmin):
        fieldsets = [
            (None,               {'fields': ['question_text']}),
            ('Date information', {'fields': ['pub_date'], 'classes': ['collapse']}),
        ]

 ```

    #### 增加关联数据

    第一种方式为和上面增加Quesiton时一样直接注册。这种方式在编辑或者添加的时候会有一个下拉框来让使用者选择这个是属于哪一个关联者。可以想到的使用场景为某一文章选择该文章的所属类型。

    第二种方式为在编辑Question的时候直接提供编辑其选项的功能：这里的编辑问题时直接编辑选项使用这种方式显然更为合理。

   ```
   from django.contrib import admin
    from .models import Choice, Question
    class ChoiceInline(admin.StackedInline):
        model = Choice
        extra = 3

    class QuestionAdmin(admin.ModelAdmin):
        fieldsets = [
            (None,               {'fields': ['question_text']}),
            ('Date information', {'fields': ['pub_date'], 'classes': ['collapse']}),
        ]
        inlines = [ChoiceInline]

    admin.site.register(Question, QuestionAdmin)

 ```

    可以将`StackedInline`替换成`TabularInline`来时显示更加简洁，表格化：

   ```
   class ChoiceInline(admin.TabularInline):
        #...

 ```

    #### 自定义admin变化列表

    默认的。django会显示模型的`str\(\)`方法作为列表的内容。但是如果我们想要显示更多的内容就需要使用`list_display`选项了.。

   ```
   class QuestionAdmin(admin.ModelAdmin):
        # ...
        list_display = ('question_text', 'pub_date'， 'was_published_recently')

 ```

    这样做了以后会在列表页显示三列来展示每一条数据。您甚至可以点击列头来根据该字段来调整排列顺序。
    增加调整model的某些方法的返回值可以调整显示在列表中的值：

   ```
   class Question(models.Model):
        # ...
        def was_published_recently(self):
            return self.pub_date >= timezone.now\(\) - datetime.timedelta(days=1)
        was_published_recently.admin_order_field = 'pub_date'
        was_published_recently.boolean = True
        was_published_recently.short_description = 'Published recently?'

可以使用`list_filter`选项来增加右侧的过滤器。例如使用`list_filter =['pub_date']`让系统自动的为我们生成”今天“，”最近7天“，”本月“，”本年“，”不限“等选项。
可以为列表的表头中添加搜索列，使用   `search_fields` 选项，例如使用`search_fields = ['question_text']` 选项来让列表中可以根据标题来搜索选项。
[调整每一页显示的数量](https://docs.djangoproject.com/en/1.8/ref/contrib/admin/#django.contrib.admin.ModelAdmin.list_per_page)