---
title: 在ASP.NET中启动SQL SERVER缓存
tags:
  - ASP.NET
  - 'C#'
  - SQL SERVER
id: 235
categories:
  - ASP.NET
  - 'C#'
  - MVC
  - SQL
  - WEBFORM
date: 2013-07-31 16:25:09
---



在小猪的一次面试中，面试官问过这样的问题，在我们打开网站的时候我们需要向数据库取数据，大家是每次都去取呢还是通过什么方法呢？

当时小猪虽然知道可以通过缓存来减少不必要的数据库访问次数，但是却并不知道怎么来完成这样的功能。这样一段时间之内一直纠结于怎么搞他呢？

这段时间小猪比较清闲，有了很多时间思考之前没时间思考的东西，所以好好的把数据库缓存这块的知识整理整理。

好吧，下面进入正题。要想使用SQL SERVER 自带的缓存功能需要以下步骤：

1\. sqlserver中使用语句启用监听服务
```
ALTER DATABASE TestDB SET ENABLE_BROKER;

 
```

如果启用失败或者很长时间一直在执行请执行以下语句，然后秒了
```
ALTER DATABASE TestDB SET NEW_BROKER WITH ROLLBACK IMMEDIATE;

ALTER DATABASE TestDB SET ENABLE_BROKER;

 
```

2\. 检查数据库是否启用监听服务
```
SELECT is_broker_enabled FROM sys.databases WHERE name = 'TestDB'

 
```

(1为已启用,0为未启用)

3\. 配置WebConfig

配置<connectionStrings>
```
<connectionStrings>
<add name="conStr" connectionString="Data Source=.;Initial Catalog=TestDB;Persist Security Info=True;User ID=sa;Password=sa" providerName="System.Data.SqlClient"/>
</connectionStrings>

 
```

<system.web> &nbsp; 节点下配置
```
<caching>
    <sqlCacheDependency enabled="true" pollTime="1000">
        <databases><add name="TestDB" connectionStringName="conStr"/>
        </databases>
    </sqlCacheDependency>
</caching>

 
```

ps:Ok，下面上示例代码
```


//定义连接字符串
string conStr = System.Configuration.ConfigurationManager.ConnectionStrings["conStr"].ConnectionString;

protected void Page_Load(object sender, EventArgs e)
{

    if (!IsPostBack)
    {

        SqlDependency.Start(conStr);//启动监听服务，ps：只需启动一次
        System.Web.Caching.SqlCacheDependencyAdmin.EnableNotifications(conStr);//设置通知的数据库连接，ps：只需设置一次
        System.Web.Caching.SqlCacheDependencyAdmin.EnableTableForNotifications(constr, "dbo.TableA");//设置通知的数据库连接和表，ps：只需设置一次
        string sql = "select Name from dbo.TableA";
        SqlConnection con = new SqlConnection(conStr);
        SqlCommand cmd = new SqlCommand(sql, con);
        SqlDataAdapter adapter = new SqlDataAdapter(cmd);

        DataSet ds = new DataSet\(\);
        adapter.Fill(ds, "cache");

        System.Web.Caching.SqlCacheDependency cd = new System.Web.Caching.SqlCacheDependency("TestDB", "dbo.TableA");//建立关联
        Cache.Insert("cache", ds, cd);
    }

}

 
```

这样初始化后，更新数据库TestDB中表dbo.TableA后，缓存Cache[&quot;cache&quot;]会失效。

另外，在其他地方我们可以这样用，一个字：方便。
```


List<Role> list = this.Cache["data"] as List<Role>;
if (list != null) { return list; }
else
{
    list = GetList\(\);//此方法是从数据库读取数据，代码就不予展示了
    System.Web.Caching.SqlCacheDependency scd = new SqlCacheDependency("flag", "T_ERP_Role");//创建数据库依赖 第一个参数是web.config配置文件中databases节点下的Name值，第二个参数数据库中被建立依赖的表的表名称
    this.Cache.Insert("data", list, scd, System.Web.Caching.Cache.NoAbsoluteExpiration, System.Web.Caching.Cache.NoSlidingExpiration, System.Web.Caching.CacheItemPriority.High, CallBack);
    //第三个参数scd 为文件依赖项或缓存键依赖项。当任何依赖项更改时，该对象即无效，并从缓存中移除。如果没有依赖项，则此参数包含 null。
    //最后一个参数CallBack： 在从缓存中移除对象时将调用的委托（如果提供）。当从缓存中删除应用程序的对象时，可使用它来通知应用程序。

    return list;
}

 
```

另外，在ASP.NET中不仅仅可以缓存SQL ，系统自带了Cache类来实现缓存

这样可以方便的自定义自己需要缓存的东西，可以参照下列文章。

[http://www.cnblogs.com/John-Connor/archive/2012/05/17/2501864.html](http://www.cnblogs.com/John-Connor/archive/2012/05/17/2501864.html)

[](http://www.cnblogs.com/John-Connor/archive/2012/05/18/2507514.html)[http://www.cnblogs.com/John-Connor/archive/2012/05/17/2506015.html](http://www.cnblogs.com/John-Connor/archive/2012/05/17/2506015.html)
[http://www.cnblogs.com/John-Connor/archive/2012/05/18/2507514.html](http://www.cnblogs.com/John-Connor/archive/2012/05/18/2507514.html) 

下面小猪需要封装单独的数据库缓存类。
[](http://www.cnblogs.com/John-Connor/archive/2012/05/18/2507514.html)[ ](http://www.cnblogs.com/John-Connor/archive/2012/05/18/2507514.html)