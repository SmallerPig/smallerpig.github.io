---
title: 错误 Please install the package Microsoft.VisualStudio.Web.CodeGeneration.Design
tags:
  - visual studio
id: 1219
comment: false
categories:
  - IDE
date: 2017-08-20 00:05:01
---

小猪今天在使用Visual Studio 2017 Community的编写ASP.NET Core 2.0程序，新建控制器的时候使用vs提供的基础功能：
1\.  在控制器文件夹右键“新建”=》“控制器”
2\. 填入控制器名称之后确定

<!-- more -->
却出现下述错误：
![](http://wx2.sinaimg.cn/mw690/88e12591gy1fipdn5e0mvj20hb07mt8q.jpg)

解决方法是打开两种
第一种打开“工具”、NuGet包管理器、程序包管理器控制台，输入下列命令：

    PM&gt; Install-Package Microsoft.VisualStudio.Web.CodeGeneration.Design

在看到下列输出的时候说明安装成功：

> PM> Install-Package Microsoft.VisualStudio.Web.CodeGeneration.Design
>     GET https://api.nuget.org/v3/registration3-gz-semver2/microsoft.visualstudio.web.codegeneration.design/index.json
>     OK https://api.nuget.org/v3/registration3-gz-semver2/microsoft.visualstudio.web.codegeneration.design/index.json 479 毫秒
>   正在还原 D:\vsproject\WebApplication1\WebApplication1\WebApplication1.csproj 的包...
>   正在安装 NuGet 程序包 Microsoft.VisualStudio.Web.CodeGeneration.Design 2.0.0。
>   正在提交还原...
>   将锁定文件写入磁盘。路径: D:\vsproject\WebApplication1\WebApplication1\obj\project.assets.json
>   D:\vsproject\WebApplication1\WebApplication1\WebApplication1.csproj 的还原在 4.66 sec 内完成。
>   已将“Microsoft.CodeAnalysis.CSharp.Workspaces 2.3.1”成功安装到 WebApplication1
>   已将“Microsoft.CodeAnalysis.Workspaces.Common 2.3.1”成功安装到 WebApplication1
>   已将“Microsoft.VisualStudio.Web.CodeGeneration 2.0.0”成功安装到 WebApplication1
>   已将“Microsoft.VisualStudio.Web.CodeGeneration.Contracts 2.0.0”成功安装到 WebApplication1
>   已将“Microsoft.VisualStudio.Web.CodeGeneration.Core 2.0.0”成功安装到 WebApplication1
>   已将“Microsoft.VisualStudio.Web.CodeGeneration.Design 2.0.0”成功安装到 WebApplication1
>   已将“Microsoft.VisualStudio.Web.CodeGeneration.EntityFrameworkCore 2.0.0”成功安装到 WebApplication1
>   已将“Microsoft.VisualStudio.Web.CodeGeneration.Templating 2.0.0”成功安装到 WebApplication1
>   已将“Microsoft.VisualStudio.Web.CodeGeneration.Utils 2.0.0”成功安装到 WebApplication1
>   已将“Microsoft.VisualStudio.Web.CodeGenerators.Mvc 2.0.0”成功安装到 WebApplication1
>   已将“NuGet.Frameworks 4.0.0”成功安装到 WebApplication1
>   已将“System.Composition 1.0.31”成功安装到 WebApplication1
>   已将“System.Composition.AttributedModel 1.0.31”成功安装到 WebApplication1
>   已将“System.Composition.Convention 1.0.31”成功安装到 WebApplication1
>   已将“System.Composition.Hosting 1.0.31”成功安装到 WebApplication1
>   已将“System.Composition.Runtime 1.0.31”成功安装到 WebApplication1
>   已将“System.Composition.TypedParts 1.0.31”成功安装到 WebApplication1
>   已将“System.Linq.Parallel 4.3.0”成功安装到 WebApplication1
>   执行 nuget 操作花费时间 4.03 sec
>   已用时间: 00:00:10.8916407

方法二为直接的图文方式：
打开工具、NuGet包管理器、管理解决方案的NuGet程序包，在新弹出的页面中搜索：`Microsoft.VisualStudio.Web.CodeGeneration.Design`，找到对应的包之后点击安装即可。