---
title: CI3.0升级记录.
tags:
  - CI
id: 859
categories:
  - CodeIgniter
date: 2015-08-10 17:03:15
---

原文地址:http://www.codeigniter.com/userguide3/changelog.html
原文为英文,如果本文有不理解或者说的不到位的地方,各位读者请参照原文理解!

另外,CI3.0的中文文档也已经有网友对其进行了翻译,具体可参考网址:[http://www.aneasystone.com/archives/2015/08/ci-doc-chinese.html](http://www.aneasystone.com/archives/2015/08/ci-doc-chinese.html "http://www.aneasystone.com/archives/2015/08/ci-doc-chinese.html")
不再支持php5.1.6 ,CI3.0开始需要php版本为5.2.4,建议使用5.4+版本

修改命名规范(类文件名首字母必须大写,其他文件首字母小写)

修改默认的数据库驱动为'mysqli'(原来的'mysql'已过期)
$_SERVER['CI_ENV'] can now be set to control the ENVIRONMENT constant.
$_SERVER['CI_ENV'] 现在可以设置来控制环境
请理解ci2.X中定义ENVIRONMENT: define('ENVIRONMENT', 'development');
与3.0中定义ENVIRONMENT的区别: define('ENVIRONMENT', isset($_SERVER['CI_ENV']) ? $_SERVER['CI_ENV'] : 'development');

增加一个可选的php-error模板

用户代理字符串中增加了安卓平台

用户平台中增加Windows 7, Windows 8, Windows 8.1, Android, Blackberry, iOS 和 PlayStation 3

用户代理字符串中增加了Fennec(Firefox for mobile)
Ability to log certain error types, not all under a threshold.
现在可以记录(log)指定的错误,而不是在指定的阈值下

mimes.php中增加了一些新支持的类型

application/config/foreign_characters.php.中添加了一些国家的特殊字符
Changed logger to only chmod when file is first created.
在第一次创建日志文件时logger只需要chmod权限

删除已经过期的SHA1类库

删除了已经过期的application/config/autoload.php中的$autoload['core'], 现在只有$autoload['libraries']中的条目才会被自动加载

删除已经过期的EXT常量
Updated all classes to be written in PHP 5 style, with visibility declarations and no var usage for properties.
使用Php5的风格重写了所有的类.

增加了异常处理

移动了错误模板到application/views/errors/ 且该值可以通过配置$config['error_views_path']来改变该位置

增加对CLI程序 non-HTML错误模板的支持

Log类移动到application/core/文件夹下,(smallerpig注:应该是在system/core/文件夹下)

全局配置文件首先加载,然后是运行环境的.而且环境配置可覆盖全局配置,这样我们可配置不同环境下的不同配置!
修改$view_folder变量的检测,如果该目录下没找到对应文件,会再次到application文件夹下寻找
路径常量: BASEPATH,APPPATH 和VIEWPATH现在定义成绝对地址!
邮箱验证的方法使用filter_var\(\)替代PCRE
Changed environment defaults to report all errors in development and only fatal ones in testing, production but only display them in development.

现在默认开发环境下会报告所有的错误信息,测试和生产环境中只显示致命的(fatal)错误信息,

更新ip_address的数据库类型的长度(16-&gt;45),以用来适配IPv6,影响到验证码辅助函数和引用通告类库.

删除了文档中的两处pdf文件(备忘录和快速引用)
Added availability checks where usage of dangerous functions like eval\(\) and exec\(\) is required.
在使用eval\(\),exec\(\)等危险的函数时进行了可用性检查

现在可以使用变量$config['log_file_extension']来修改日志文件的后缀!

添加变量$config['standardize_newlines']用来表示是否转换标准化换行符(lunix\n,windows:

).默认值为FALSE.

添加$config['compooser_autoload']用来配置是否自动加载Composer模块

取消URI类库中的对"特殊字符串-&gt;HTML实体"的自动转换

更改类或者文件的加载时的Log信息级别为info(原为debug),这样在$config['log_threshold']设置为2的时候不至于把日志文件搞乱.