---
title: 使用CodeIgniter的购物车类  一
id: 554
categories:
  - CodeIgniter
  - PHP
date: 2015-04-17 15:55:19
tags:
---

相关文章:

*   [使用CodeIgniter的购物车类 一](http://www.smallerpig.com/554.html "使用CodeIgniter的购物车类  一")
*   [使用CodeIgniter的购物车类 二](http://www.smallerpig.com/570.html "使用CodeIgniter的购物车类 二")
CI框架本身提供了很多类库来帮助我们简化网站的开发.今天我来分享在项目中使用购物车类的过程.希望对后来者有用!

购物车类允许项目被添加到session中，session在用户浏览你的网站期间都保持有效状态。这些项目能够以标准的 "购物车" 格式被检索和显示，并允许用户更新数量或者从购物车中移除项目。

请注意购物车类只提供核心的"购物车"功能。它不提供配送、信用卡授权或者其它处理组件.

因为购物车类利用了CI的session类把购物车信息保存到数据库中,所以在使用购物车类之前,你必须根据session类文档中的说明来创建数据库表,并且在application/config/config.php 文件中把session相关参数设置为使用数据库
``` 

 $config['sess_use_database']	= TRUE;
$config['sess_table_name']	= 'ci_sessions';


 ```
创建数据库表的sql语句
``` 

 CREATE TABLE IF NOT EXISTS  `ci_sessions` (
  session_id varchar(40) DEFAULT '0' NOT NULL,
  ip_address varchar(45) DEFAULT '0' NOT NULL,
  user_agent varchar(120) NOT NULL,
  last_activity int(10) unsigned DEFAULT 0 NOT NULL,
  user_data text DEFAULT '' NOT NULL,
  PRIMARY KEY (session_id),
  KEY `last_activity_idx` (`last_activity`)
);


 ```
&nbsp;

为了在你的控制器构造函数中初始化购物车类,请使用$this-&gt;load-&gt;library函数
``` 

 $this-&gt;load-&gt;library('cart');

 ```
说明: 购物车类会自动加载和初始化Session类，因此除非你在别处要用到session，否则你不需要再次加载Session类。

### 将一个项目添加到购物车

要添加项目到购物车,只需将一个包含了商品信息的数组传递给$this-&gt;cart-&gt;insert\(\)函数即可,就像下面这样:
``` 

 $data = array(
               'id'      =&gt; 'sku_123ABC',
               'qty'     =&gt; 1,
               'price'   =&gt; 39.95,
               'name'    =&gt; 'T-Shirt',
               'options' =&gt; array('Size' =&gt; 'L', 'Color' =&gt; 'Red')
            );

$this-&gt;cart-&gt;insert($data);


 ```
五个保留的索引分别是:

*   id - 你的商店里的每件商品都必须有一个唯一的标识符(identifier)。典型的标识符是 "sku"(译者注：库存量单位) 或者其它类似的标识符。
*   qty - 购买的数量(quantity)。
*   price - 商品的价格(price)。
*   name - 商品的名称(name)。
*   options - 标识商品的任何附加属性。必须通过数组来传递。
除以上五个索引外，还有两个保留字：rowid 和 subtotal。它们是购物车类内部使用的，因此，往购物车中插入数据时，请不要使用这些词作为索引。

你的数组可能包含附加的数据。你的数组中包含的所有数据都会被存储到session中。然而，最好的方式是标准化你所有商品的数据，这样更方便你在表格中显示它们。

如果成功的插入一条数据后，insert\(\)方法将会返回一个id值（$rowid）

完整的php代码如下.
``` 

 class Cart extends CI_Controller{
    public  function Add\(\){
        $this-&gt;load-&gt;library("cart");
        $data = array(
            'id'      =&gt; 'sku_123ABC',
            'qty'     =&gt; 1,
            'price'   =&gt; 39.95,
            'name'    =&gt; 'T-Shirt',
            'options' =&gt; array('Size' =&gt; 'L', 'Color' =&gt; 'Red')
        );
        $this-&gt;cart-&gt;insert($data);

        echo "add\(\) called";
    }
}


 ```
在浏览器中访问地址:[http://127.0.0.1/cart/add](http://127.0.0.1/cart/add "http://127.0.0.1/cart/add") 我们可以看到正确的调用:

[![image](http://www.smallerpig.com/wp-content/uploads/2015/04/image_thumb.png "image")](http://www.smallerpig.com/wp-content/uploads/2015/04/image.png)

.为了查看先有购物车的数据,我们编写一个单独的方法来显示数据.
``` 

  
    public function show\(\){
        $data = $this-&gt;cart-&gt;contents\(\);
        echo "&gt;pre&gt;";
        print_r($data);
    }


 ```
在浏览器里查看地址[http://127.0.0.1/cart/show](http://127.0.0.1/cart/show "http://127.0.0.1/cart/show"),可以看到我们的刚才的数据已经在里面

[![image](http://www.smallerpig.com/wp-content/uploads/2015/04/image3_thumb.png "image")](http://www.smallerpig.com/wp-content/uploads/2015/04/image3.png)

如果你可以查看数据库的话,打开数据库你也可以在表ci_session中看到已经插入了一条数据

[![image](http://www.smallerpig.com/wp-content/uploads/2015/04/image_thumb2.png "image")](http://www.smallerpig.com/wp-content/uploads/2015/04/image2.png)

&nbsp;

**什么是 Row ID?**  当一个项目被添加到购物车时，程序所生成的那个唯一的标识符就是row ID。创建唯一ID的理由是，当购物车中相同的商品有不同的选项时，购物车就能够对它们进行管理。

比如说，有人购买了两件相同的T-shirt (相同的商品ID)，但是尺寸不同。商品ID(以及其它属性)都会完全一样，因为它们是相同的T-shirt。它们唯一的差别就是尺寸不同。因此购物车必须想办法来区分它们，这样才能独立地管理这两件尺寸不同的T-shirt。而基于商品ID和其它相关选项信息来创建一个唯一的 "row ID" 就能解决这个问题。

在几乎所有情况下，更新购物车都将是用户通过 "查看购物车" 页面来实现的，因此对开发者来说，不必太担心"row ID"，只要保证你的 "查看购物车" 页面中的一个隐藏表单字段包含了这个信息，并且确保它能被传递给表单提交时所调用的更新函数就行了。请仔细分析上面的 "查看购物车" 页面的结构以获取更多信息。

#### 

### 将多个项目添加到购物车

通过下面这种多维数组的方式，可以一次性添加多个产品到购物车中。当你希望允许用户选择同一页面中的多个项目时，这就非常有用了。
``` 

 	$data = array(
               array(
                       'id'      =&gt; 'sku_123ABC',
                       'qty'     =&gt; 1,
                       'price'   =&gt; 39.95,
                       'name'    =&gt; 'T-Shirt',
                       'options' =&gt; array('Size' =&gt; 'L', 'Color' =&gt; 'Red')
                    ),
               array(
                       'id'      =&gt; 'sku_567ZYX',
                       'qty'     =&gt; 1,
                       'price'   =&gt; 9.95,
                       'name'    =&gt; 'Coffee Mug'
                    ),
               array(
                       'id'      =&gt; 'sku_965QRS',
                       'qty'     =&gt; 1,
                       'price'   =&gt; 29.95,
                       'name'    =&gt; 'Shot Glass'
                    )
            );

	$this-&gt;cart-&gt;insert($data);


 ```

### 更新购物车

为了更新购物车中的信息，你必须将一个包含了 Row ID 和数量(quantity)的数组传递给 $this-&gt;cart-&gt;update\(\) 函数:
``` 

 	$data = array(
               'rowid' =&gt; 'b99ccdf16028f015540f341130b6d8ec',
               'qty'   =&gt; 3
            );

	$this-&gt;cart-&gt;update($data); 

	// 或者是一个多维数组

	$data = array(
               array(
                       'rowid'   =&gt; 'b99ccdf16028f015540f341130b6d8ec',
                       'qty'     =&gt; 3
                    ),
               array(
                       'rowid'   =&gt; 'xw82g9q3r495893iajdh473990rikw23',
                       'qty'     =&gt; 4
                    ),
               array(
                       'rowid'   =&gt; 'fh4kdkkkaoe30njgoe92rkdkkobec333',
                       'qty'     =&gt; 2
                    )
            );

	$this-&gt;cart-&gt;update($data);


 ```

### 删除购物车条目

删除购物车条目其实就是将某一条数据的qty置为0,例如
``` 

 	$data = array(
               'rowid' =&gt; 'b99ccdf16028f015540f341130b6d8ec',
               'qty'   =&gt; 0
            );
	$this-&gt;cart-&gt;update($data);


 ```