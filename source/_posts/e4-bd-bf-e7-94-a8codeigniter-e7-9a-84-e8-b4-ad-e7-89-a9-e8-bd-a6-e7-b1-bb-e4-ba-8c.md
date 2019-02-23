---
title: 使用CodeIgniter的购物车类 二
id: 570
categories:
  - CodeIgniter
  - PHP
  - WEB开发
date: 2015-04-19 16:08:59
tags:
---

在前一篇博客中,小猪给大家分享了[如何在CI中使用购物车类](http://www.smallerpig.com/554.html)的一些基础.但是没有结合数据库中的具体数据来进行演示.今天我就来结合数据库中的数据来进一步演示CI的购物车类的一些高级用法.

## 准备工作

首先在数据库表中新建我们的商品表.
``` 

 CREATE TABLE IF NOT EXISTS `products` (
  `id` int(11) NOT NULL AUTO_INCREMENT,
  `name` varchar(255) NOT NULL,
  `price` decimal(13,2) NOT NULL,
  `image` varchar(255) NOT NULL,
  `option_name` varchar(255) NOT NULL,
  `option_values` varchar(255) NOT NULL,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB  DEFAULT CHARSET=latin1 AUTO_INCREMENT=1 ;


 ```
其中,id作为上面的表示,name为商品的名称,price为商品的价格.image为商品的图片地址,option_name为商品的属性,例如颜色,尺码等.option_values为商品的属性值.

作为演示,这边的商品属性只设置成一个.不能像淘宝那样为每一个商品动态的随意添加属性.

然后插入三条演示数据
``` 

 INSERT INTO `products` (`id`, `name`, `price`, `image`, `option_name`, `option_values`) VALUES
(1, 'Rubber Ducky', '2.99', 'ducky.jpg', '', ''),
(2, 'Horse', '4500.00', 'horse.jpg', 'Color', 'white,black,brown'),
(3, 'T-Shirt', '12.99', 'tshirt.jpg', 'Size', 'small,medium,large');


 ```
其中第一条数据没有属性,第二条商品对应的属性为color,属性值为white,black,brown 三个值之间以因为逗号(,)隔开.第三条数据的属性值为尺码(size),分别对应值为大中小.同样以逗号隔开.为了提高用户体验,我们分别给每个商品配了一幅图片,放在网站根目录的images文件夹下.

## 编写controller代码

``` 

 function index\(\) {

	$this-&gt;load-&gt;model('Products_model');

	$data['products'] = $this-&gt;Products_model-&gt;get_all\(\);

	$this-&gt;load-&gt;view('products', $data);
}


 ```
上述代码中加载了Products_model模型,并调用了其get_all\(\)方法.然后加载了视图products文件.

所以在models文件夹中新建model类文件Products_model.php.编写方法get_all\(\):

## 编写模型类

``` 

 &lt;?php
class Products_model extends CI_Model {

	function get_all\(\) {

		$results = $this-&gt;db-&gt;get('products')-&gt;result\(\);

		foreach ($results as &amp;$result) {

			if ($result-&gt;option_values) {
				$result-&gt;option_values = explode(',',$result-&gt;option_values);
			}

		}

		return $results;

	}

}


 ```
注意上述代码中,我们判断了商品是否包含option_valus属性,如果有的话,使用explode函数将其转化成一个数组,这样可以在前台页面直接使用数组的方式来访问商品的属性值.

编写视图文件:在view文件夹中新建products.php视图文件用来展示页面:

## 编写视图文件

``` 

 &lt;!DOCTYPE HTML&gt;
&lt;html lang="en-US"&gt;
&lt;head&gt;
    &lt;title&gt;Shop&lt;/title&gt;
    &lt;meta charset="UTF-8"&gt;
    &lt;style type="text/css"&gt;
        body {
            font: 13px Arial;
        }
        #products {
            text-align: center; float: left;
        }
        #products ul {
            list-style-type: none; margin: 0px;
        }
        #products li {
            width: 150px; padding: 4px; margin: 8px;
            border: 1px solid #ddd; background-color: #eee;
            -moz-border-radius: 4px; -webkit-border-radius: 4px;
        }
        #products .name {
            font-size: 15px; margin: 5px;
        }
        #products .price {
            margin: 5px;
        }
        #products .option {
            margin: 5px;
        }

        #cart {
            padding: 4px; margin: 8px; float: left;
            border: 1px solid #ddd; background-color: #eee;
            -moz-border-radius: 4px; -webkit-border-radius: 4px;
        }
        #cart table {
            width: 320px; border-collapse: collapse;
            text-align: left;
        }
        #cart th {
            border-bottom: 1px solid #aaa;
        }
        #cart caption {
            font-size: 15px; height: 30px; text-align: left;
        }
        #cart .total {
            height: 40px;
        }
        #cart .remove a {
            color: red;
        }
    &lt;/style&gt;
&lt;/head&gt;
&lt;body&gt;
&lt;div id="products"&gt;
    &lt;ul&gt;
        &lt;?php foreach ($products as $product): ?&gt;
            &lt;li&gt;
                &lt;?php echo form_open('cart/add'); ?&gt;
                &lt;div class="name"&gt;&lt;?php echo $product-&gt;name; ?&gt;&lt;/div&gt;
                &lt;div class="thumb"&gt;
                    &lt;?php echo img(array(
                        'src' =&gt; 'images/' . $product-&gt;image,
                        'class' =&gt; 'thumb',
                        'alt' =&gt; $product-&gt;name
                    )); ?&gt;
                &lt;/div&gt;
                &lt;div class="price"&gt;$&lt;?php echo $product-&gt;price; ?&gt;&lt;/div&gt;
                &lt;div class="option"&gt;
                    &lt;?php if ($product-&gt;option_name): ?&gt;
                        &lt;?php echo form_label($product-&gt;option_name, 'option_'. $product-&gt;id); ?&gt;
                        &lt;?php echo form_dropdown(
                            $product-&gt;option_name,
                            $product-&gt;option_values,
                            NULL,
                            'id="option_'. $product-&gt;id.'"'
                        ); ?&gt;
                    &lt;?php endif; ?&gt;
                &lt;/div&gt;

                &lt;?php echo form_hidden('id', $product-&gt;id); ?&gt;
                &lt;?php echo form_submit('action', 'Add to Cart'); ?&gt;
                &lt;?php echo form_close\(\); ?&gt;
            &lt;/li&gt;
        &lt;?php endforeach; ?&gt;
    &lt;/ul&gt;
&lt;/div&gt;

&lt;?php if ($cart = $this-&gt;cart-&gt;contents\(\)): ?&gt;
    &lt;div id="cart"&gt;
        &lt;table&gt;
            &lt;caption&gt;Shopping Cart&lt;/caption&gt;
            &lt;thead&gt;
            &lt;tr&gt;
                &lt;th&gt;Item Name&lt;/th&gt;
                &lt;th&gt;Option&lt;/th&gt;
                &lt;th&gt;Price&lt;/th&gt;
                &lt;th&gt;&lt;/th&gt;
            &lt;/tr&gt;
            &lt;/thead&gt;
            &lt;?php foreach ($cart as $item): ?&gt;
                &lt;tr&gt;
                    &lt;td&gt;&lt;?php echo $item['name']; ?&gt;&lt;/td&gt;
                    &lt;td&gt;
                        &lt;?php if ($this-&gt;cart-&gt;has_options($item['rowid'])) {
                            foreach ($this-&gt;cart-&gt;product_options($item['rowid']) as $option =&gt; $value) {
                                echo $option . ": &lt;em&gt;" . $value . "&lt;/em&gt;";
                            }

                        } ?&gt;
                    &lt;/td&gt;
                    &lt;td&gt;$&lt;?php echo $item['subtotal']; ?&gt;&lt;/td&gt;
                    &lt;td class="remove"&gt;
                        &lt;?php echo anchor('cart/remove/'.$item['rowid'],'X'); ?&gt;
                    &lt;/td&gt;
                &lt;/tr&gt;
            &lt;?php endforeach; ?&gt;
            &lt;tr class="total"&gt;
                &lt;td colspan="2"&gt;&lt;strong&gt;Total&lt;/strong&gt;&lt;/td&gt;
                &lt;td&gt;$&lt;?php echo $this-&gt;cart-&gt;total\(\); ?&gt;&lt;/td&gt;
            &lt;/tr&gt;
        &lt;/table&gt;
    &lt;/div&gt;
&lt;?php endif; ?&gt;
&lt;/body&gt;
&lt;/html&gt;

 ```
注意上述视图文件中使用了部分表单辅助函数.

打开浏览器访问index方法.我们可以看到页面效果:

[![image](http://www.smallerpig.com/wp-content/uploads/2015/04/image_thumb3.png "image")](http://www.smallerpig.com/wp-content/uploads/2015/04/image4.png)

其中第一个商品没有属性,第二个和第三个商品因为有属性,所以我们生成了一个下拉框来给用户选择,每一个商品都有一个对应的按钮将当前选择的商品以及属性加入到购物车.

但是我们现在暂时还没有增加到购物车的代码.所以我们完善controller代码:增加一个添加到购物车的函数:

## 编写添加方法

``` 

 function add\(\) {

	$this-&gt;load-&gt;model('Products_model');

	$product = $this-&gt;Products_model-&gt;get($this-&gt;input-&gt;post('id'));

	$insert = array(
		'id' =&gt; $this-&gt;input-&gt;post('id'),
		'qty' =&gt; 1,
		'price' =&gt; $product-&gt;price,
		'name' =&gt; $product-&gt;name
	);
	if ($product-&gt;option_name) {
		$insert['options'] = array(
			$product-&gt;option_name =&gt; $product-&gt;option_values[$this-&gt;input-&gt;post($product-&gt;option_name)]
		);
	}

	$this-&gt;cart-&gt;insert($insert);

	redirect('cart');

}


 ```
其中用到了模型类中的根据id来获取一条商品信息的方法:我们在model类中新增函数:
``` 

 function get($id) {

	$results = $this-&gt;db-&gt;get_where('products', array('id' =&gt; $id))-&gt;result\(\);
	$result = $results[0];

	if ($result-&gt;option_values) {
		$result-&gt;option_values = explode(',',$result-&gt;option_values);
	}

	return $result;
}



 ```
和上面一样的,我们判断了商品是否含有属性值,如果有将其转化成数组.并返回

重新运行程序并尝试点击添加按钮!

[![image](http://www.smallerpig.com/wp-content/uploads/2015/04/image_thumb4.png "image")](http://www.smallerpig.com/wp-content/uploads/2015/04/image5.png)

可以看到我们成功的将商品加入到了购物车类.

如果要删除购物车中的某商品,我们需要将该条<span style="color: #ff0000;">购物车商品</span>的rowid的数量转成0即可,这里一定要注意的就是rowid而不是商品本身的id,这个我们在第一篇中已经有过具体的介绍.

在控制器中添加方法remove

## 编写删除方法

``` 

 function remove($rowid) {

    $this-&gt;cart-&gt;update(array(
        'rowid' =&gt; $rowid,
        'qty' =&gt; 0
    ));

    redirect('cart');
}


 ```
然后点击某条数据的删除按钮:

[![image](http://www.smallerpig.com/wp-content/uploads/2015/04/image_thumb5.png "image")](http://www.smallerpig.com/wp-content/uploads/2015/04/image6.png)

可以看到我们的商品已经从购物车中删除.

## 总结

接下来的工作可能就是需要处理支付的场景了!