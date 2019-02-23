---
title: ASP.NET MVC 上传大文件时404
id: 343
categories:
  - ASP.NET
  - 'C#'
  - HTML
  - MVC
  - WEB开发
date: 2013-11-12 08:39:59
tags:
---

前一段时间会员的上传组件改用FLASH的swfupload来上传，既能很友好的显示上传进度，又能完全满足大文件的上传。

后来服务器升级到windows 2008,改为IIS7后，上传文件一旦超过30M时，就出现404错误，而且是是上传进度达到100%之后，真是让人难思其解。


反复测试，发现FLASH上传文件到并没有正确的执行.NET程序，也就是.NET程序本身有问题;

但小于30M又是一切OK，难道是上传的文件大小有所限制？

检查web.config的httpRuntime ：
```
<httpRuntime maxRequestLength="2097151" executionTimeout="50000" />
```

已经是很大值了。

因为无法正确得到详细的错误信息，就用一个普通的FORM提交一个FileUpload测试，原来真是web.config的设置问题：

# 报错信息：

最可能的原因: Web 服务器上的请求筛选被配置为拒绝该请求，因为内容长度超过配置的值。 可尝试的操作: 确认 applicationhost.config 或 web.config 文件中的 configuration/system.webServer/security/requestFiltering/requestLimits@maxAllowedContentLength 设置。 

链接和更多信息
这是一项安全功能。请不要更改此功能，除非您完全清楚更改的影响范围。您可以配置 IIS 7.0 服务器以拒绝内容长度大于指定值的请求。如果请求的内容长度大于所配置的长度，便会返回此错误。如果需要增加内容长度，请修改 configuration/system.webServer/security/requestFiltering/requestLimits@maxAllowedContentLength 设置。

# 解决方案

原来IIS7的上传文件大小，即便是在经典模式下，也一定要在system.webServer里设置，加上去就OK了：
```
    <system.webServer>

    
      <security>
    
        <requestFiltering >
    
          <requestLimits maxAllowedContentLength="1073741824" ></requestLimits>
    
        </requestFiltering>
    
      </security>
    
    </system.webServer>
```