---
title: MySQL 分表存储
tags:
  - mysql
id: 1062
categories:
  - SQL
date: 2016-08-16 16:35:15
---

一般我们项目中如果数据量特别大的话通常会考虑将某一表数据进行分表处理，例如：我们的用户访问日志。如果将该数据始终保存在一张表的话那么久而久之这张表的数据量会特别大，导致查询效率降低。这个时候就需要考虑将该数据分别存储到多张表中。
而日志数据具有典型的如下特点：
1\. 数据量大。
2\. 每天每月都稳定输出。 
3\. 对数据的操作在插入之后大部分只有查询，很少有编辑或者删除操作。

MySQL数据库提供了数据分表的方式。

* * *

## MERGE存储引擎

我们在使用SQL语句`Create table`创建表时可以指定数据表的存储引擎。当我们使用语句`ENGINE=MEGRE`或者`ENGINE=MRG_MyISAM`申明存储引擎时，即代表该数据表使用MERGE存储引擎存储数据。该数据表可以是其他特定`ENGINE=MEGRE`类型数据表的集合。不过这些所谓特定的数据表的表结构和索引必须完全一致（不过定义的顺序可以不同）。特别的表的`AVG_ROW_LENGTH, MAX_ROWS,PACK_KEYS`属性可以不同。
```
mysql> CREATE TABLE t1 (
    ->    a INT NOT NULL AUTO_INCREMENT PRIMARY KEY,
    ->    message CHAR(20)) ENGINE=MyISAM;
mysql> CREATE TABLE t2 (
    ->    a INT NOT NULL AUTO_INCREMENT PRIMARY KEY,
    ->    message CHAR(20)) ENGINE=MyISAM;
mysql> INSERT INTO t1 (message) VALUES ('Testing'),('table'),('t1');
mysql> INSERT INTO t2 (message) VALUES ('Testing'),('table'),('t2');
mysql> CREATE TABLE total (
    ->    a INT NOT NULL AUTO_INCREMENT,
    ->    message CHAR(20), INDEX(a))
    ->    ENGINE=MERGE UNION=(t1,t2) INSERT_METHOD=LAST;
```
上述代码是官方文档中给出的示例代码。其创建了两个分表：t1和t2，一个总表total。分别给t1和t2中插入数据，然后在total表中查询，可以发现在total表中可以查询到分别插入t1和t2的数据。

我们可以在total表中使用哦SELECT,DELETE,UPDATE和INSERT操作，不过必须要拥有各分表的SELECT,DELETE 和UPDATE权限。

在创建MERGE表时，使用参数`UNION`对表进行整合，例如上面代码的`UNION=(t1, t2)`。也可以使用`INSERT_METHOD`参数来申明如果在MERGE表中插入数据，是将数据插入到第一张表还是最后一张表中，如果`INSERT_METHOD=NO`，那么MEGRE表中将不允许插入数据，只允许插入在各分表当中。

不过另外一个值得注意的地方是上述代码中我们在分区表中并没有定义索引，但是在total表中我们定义了`INDEX(a)`并且没有定义`PRIMARY KEY`。这是因为在总表中的数据并不能保证也不检查数据的唯一性，也就是在各分表中是允许存在完全相同的数据的。

* * *

## 编辑映射

如果我们在使用过程中新建了需要加入总表的分表，这时候通常有两种方式：

1.  删掉总表重新创建
2.  使用`ALTER TABLE tbl_name UNION=(……)`来重新标记需要映射的分表。

> 如果我们不小心写成`UNION=\(\)`（括号中为空），会发现这张表将为空，并且插入不进去数据，因为实际上本身总表不存储数据，这样插入数据时MYSQL也就不知道讲数据插入到哪了。

* * *

## 一些规则

总表和分表的定义以及索引需要保证大部分的一致。MySQL不会在创建总表的时候去检查分表的设置有没有错误，只有在打开查询的时候才会进行判断有没有存在错误。这也导致我们有可能在创建分表的时候不存在错误，然后某天编辑了分表，从而导致主表无法使用的情况。所以我们的总表和分表必须满足下列规则

*   总表和分表的列数必须相同。
*   总表和分表的列顺序需要一致。
*   另外，总表和分表的列规则必须满足以下规则

*   列类型必须相同
*   列的长度必须相同
*   列的内容可以为NULL
*   分表的索引数量必须大于等于总表的索引数量。分表的索引数量可以大于总表，但不可以少于总表。

表索引的相关规则:

*   总表和分表的索引类型必须一致
*   总表和分表的符合索引部分必须一致
*   针对索引部分(index part)的额外检查
*   索引部分长度必须一致
*   索引部分类型必须一致
*   索引部分的语言设置必须一致
*   可以为空

如果我们的总表在使用过程中无法打开了或者无法使用，我们亦可以使用命令：`CHECK TABLE`来显示到底是哪一个分表出现了问题。