---
title: Visual Studio 2012中的为创建类时的添加注释模板
tags:
  - visual studio
id: 351
categories:
  - 'C#'
  - IDE
date: 2013-11-27 15:54:27
---

    我们往往需要给类添加注释，我们可以把注释块复制出来，放到文件中，然后在需要的时候，复制、粘贴。这样的重复劳动增加了程序员的体力劳动，而VS中给我们提供了项模版，我们只需要在其中修改一点点模版就能达到这样的效果。

    &nbsp;

    首先，找到类模版的位置：C:Program Files (x86)Microsoft Visual Studio 11.0Common7IDEItemTemplatesCSharpCode2052Class，打开Class.cs，为其添加头注释：

``` 

 
/* =======================================================================
* 类名称：$safeitemrootname$
* 类描述：
* 创建人：$username$
* 创建时间：$time$
* 修改人：
* 修改时间：
* 修改备注：
* @version 1.0
* =======================================================================*/
using System;
using System.Collections.Generic;
$if$ ($targetframeworkversion$ &gt;= 3.5)using System.Linq;
$endif$using System.Text;
$if$ ($targetframeworkversion$ &gt;= 4.5)using System.Threading.Tasks;
$endif$
namespace $rootnamespace$
{
    /// &lt;summary&gt;
    /// $safeitemrootname$
    /// &lt;/summary&gt;
    public class $safeitemrootname$
    {
    }
}

 ```

    这里的$safeitemrootname$和$username$是由系统提供给我们的，还有一些其他参数：

    参数 说明

    clrversion 公共语言运行库 (CLR) 的当前版本。

    itemname 用户在添加新项对话框中提供的名称。

    machinename 当前的计算机名称（例如，Computer01）。

    projectname 用户在新建项目对话框中提供的名称。

    registeredorganization HKLMSoftwareMicrosoftWindows NTCurrentVersionRegisteredOrganization 中的注册表项值。

    rootnamespace 当前项目的根命名空间。此参数用于替换正向项目中添加的项中的命名空间。

    safeitemname 用户在&ldquo;添加新项&rdquo;对话框中提供的名称，名称中移除了所有不安全的字符和空格。

    safeprojectname 用户在&ldquo;新建项目&rdquo;对话框中提供的名称，名称中移除了所有不安全的字符和空格。

    time 以 DD/MM/YYYY 00:00:00 格式表示的当前时间。

    userdomain 当前的用户域。

    username 当前的用户名。

    year 以 YYYY 格式表示的当前年份。

    &nbsp;

    &nbsp;