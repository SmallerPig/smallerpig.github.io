---
title: 设置正确的post数据格式
id: 432
categories:
  - 'C#'
  - MVC
  - WEB开发
date: 2014-02-26 09:55:59
tags:
---

之前一直使用苏飞的HttpHelper类来访问网络，用起来一直感觉很爽。使用其工具直接生成访问代码很是方便。直到昨天下午做到需要使用wpf来post两个字段数据到服务器,服务器使用ASP.NET MVC来接收表单数据时出现了问题。

首先：按照正常使用习惯来生成的代码是：
```
HttpHelper http = new HttpHelper\(\);
HttpItem item = new HttpItem\(\)
{
   URL = "http://localhost:2250/api/login",//URL     必需项    
    Method = "post",//URL     可选项 默认为Get   
    IsToLower = false,//得到的HTML代码是否转成小写     可选项默认转小写   
    Cookie = "",//字符串Cookie     可选项   
    Referer ="",//来源URL     可选项   
    Postdata = "username=1@1.com&amp;password=123456",//Post数据     可选项GET时不需要写   
    Timeout = 100000,//连接超时时间     可选项默认为100000    
    ReadWriteTimeout = 30000,//写入Post数据超时时间     可选项默认为30000   
    UserAgent = "Mozilla/5.0 (compatible; MSIE 9.0; Windows NT 6.1; Trident/5.0)",//用户的浏览器类型，版本，操作系统     可选项有默认值   
    ContentType = "text/html",//返回类型    可选项有默认值   
    Allowautoredirect = false,//是否根据301跳转     可选项   
    //CerPath = "d:123.cer",//证书绝对路径     可选项不需要证书时可以不写这个参数   
    //Connectionlimit = 1024,//最大连接数     可选项 默认为1024    
    ProxyIp = "",//代理服务器ID     可选项 不需要代理 时可以不设置这三个参数    
    //ProxyPwd = "123456",//代理服务器密码     可选项    
    //ProxyUserName = "administrator",//代理服务器账户名     可选项   
};
HttpResult result = http.GetHtml(item);
string html = result.Html;
string cookie = result.Cookie;

```
&nbsp;

服务器来接受到请求，但是接受到的username 和password字段都是null。 小猪捣鼓了一个下午一直认为是post数据的问题。尝试将其改成
```
string postData = "{username=123&amp;password=123}"
```
甚至是
```
string postData = "{username='123'&amp;password='123'}"
```
问题依旧啊！害得我昨天晚上一生气玩dota到12点~:-)

期间自己编写html代码来测试数据
``` 
<html>
<head>
</head>
<body>
<div>
<form action="http://localhost:2250/api/login" method ="Post">
    <input type = "text" name = "username"/>
    <input type = "password" name = "password"/>
    <input type = "submit" >
</form>

</div>
</body>
</html>
```
&nbsp;

这样测试是能够接受到数据的。各种百度、谷歌、官方论坛找到下列结果[http://www.sufeinet.com/forum.php?mod=viewthread&amp;tid=7497&amp;highlight=post%2B%B1%ED%B5%A5](http://www.sufeinet.com/forum.php?mod=viewthread&amp;tid=7497&amp;highlight=post%2B%B1%ED%B5%A5 "http://www.sufeinet.com/forum.php?mod=viewthread&amp;tid=7497&amp;highlight=post%2B%B1%ED%B5%A5")

原来需要将请求的ContenType，这样HttpWebRequest类会将post数据当成表单数据来处理，这样在ASP.NET MVC后台就可以成功接受到数据。
```
ContentType = "application/x-www-form-urlencoded",
```
&nbsp;