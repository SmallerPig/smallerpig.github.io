---
title: CI中使用layout文件(类似模板效果)
tags:
  - CI
id: 653
categories:
  - CodeIgniter
  - PHP
  - WEB开发
date: 2015-05-04 20:12:09
---

CI框架本身不提供类似模板的功能,所以官方文档中并没有相关的介绍!

虽然在视图的文档中给出了这样的代码:
```
<?php

class Page extends CI_Controller {

   function index\(\)
   {
      $data['title'] = 'Your title';
      $data['message'] = 'Your message';
      $this->load->view('header',$data);
      $this->load->view('content');
      $this->load->view('footer');
   }

}
?>

```
该段代码是利用了同一个控制器可以加载多个视图的特性.将html文档的头部,内容部分以及页脚部分分别放三个不同的文件中,这样在一定程度上能够满足我们的需求.但还是不够友好,

小猪今天分享的是一个类库,利用该类库可以实现类似模板的功能.在application项目文件夹的libraries文件夹中新建Layout.php文件:
```
class Layout
{

    var $CI;
    var $layout;

    function Layout($layout = "_layout")
    {
        $this->CI =&amp; get_instance\(\);
        $this->layout = 'share/' . $layout;
    }

    function setLayout($layout)
    {
        $this->layout = $layout;
    }

    function view($view, $data=null, $return=false)
    {
        $data['content_for_layout'] = $this->CI->load->view($view,$data,true);

        if($return)
        {
            $output = $this->CI->load->view($this->layout,$data, true);
            return $output;
        }
        else
        {
            $this->CI->load->view($this->layout,$data, false);
        }
    }
}


```
在application项目文件夹的view文件夹下新建share文件夹,然后新建_layout.php文件!如果有需求可以新建多个视图文件:

输入文件内容:
```
<!doctype html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Document</title>
</head>
<body>
<?php echo $content_for_layout  ?>
</body>
</html>


```
代码中使用了php变量content_for_layout.而这个变量的值是在我们新建的Layout类库的view方法里赋值的,其值为$this->CI->load->view($view,$data,true).

意思就是将加载了数据的视图文件赋值给变量$content_for_layout,然后再一次的加载我们的模板页,也就是layout.php文件!

所以,我们在控制器里写入这样的代码:
```
function test\(\){
    $data["content"] = "hi smallerpig! this is the content from controller!";
    $this->load->library('layout');
    $this->layout->view("test",$data);
}

```
其中用到了一个视图文件test.php:
```
<div>-- <?php echo $content ?> -- </div>

```
这样最终我们页面输出的html文档将会是:
```
<!doctype html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Document</title>
</head>
<body>
<div>-- hi smallerpig! this is the content from controller! -- </div>
</body>
</html>

```
最终我们完成了将整个页面相同的html代码先提取到了layout(share/_layout.php)文件中,然后将会变的内容放到单独的视图(test.php)文件!而且类库还兼容了原来的类库写法,即view方法的第三个参数类型为bool类型,如果传入为true,则将返回视图结果字符串!