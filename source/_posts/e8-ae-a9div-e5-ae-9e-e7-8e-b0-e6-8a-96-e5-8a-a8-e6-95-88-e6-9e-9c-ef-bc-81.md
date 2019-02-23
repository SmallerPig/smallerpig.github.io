---
title: 让DIV实现抖动效果！
id: 401
categories:
  - CSS
  - HTML
  - JS
  - WEB开发
date: 2014-01-16 17:23:38
tags:
---

``` 

 &lt;html&gt;
&lt;head&gt;
&lt;meta http-equiv="Content-Type" content="text/html; charset=utf-8" /&gt;
&lt;title&gt;JavaScript层抖动效果&lt;/title&gt;
&lt;style type="text/css"&gt;
#body{text-align:center;}
#test{width:200px;position:absolute;margin:10px auto;height:100px;border:2px dotted red;text-align:center}
&lt;/style&gt;
&lt;/head&gt;
&lt;body&gt;
&lt;div style="margin:10px 200px"&gt;
    &lt;div&gt;
        &lt;input type="button" value="~点这里让我抖抖吧~" onclick="nn.start\(\)" /&gt;&lt;/div&gt;
    &lt;div&gt;
        &lt;input type="button" value="晃晕了，我不抖了！" onclick="nn.stop\(\)" /&gt;&lt;/div&gt;
    &lt;div id="test"&gt;
        &lt;br&gt;
        &lt;/div&gt;
&lt;/div&gt;
&lt;p&gt;　&lt;/p&gt;
&lt;p&gt;　&lt;/p&gt;
&lt;/body&gt;
&lt;/html&gt;
&lt;script type="text/javascript"&gt;
 var m=document.getElementById("test");
function SKclass (obj,Rate,speed) {
     var oL=obj.offsetLeft;
     var oT=obj.offsetTop;
     this.stop=null;
     this.oTime=null;
     var om=this;
     this.start=function\(\){
             if(parseInt(obj.style.left)==oL-2){
                obj.style.top=oT+2+"px";
                setTimeout(function\(\){obj.style.left=oL+2+"px"},Rate)
             }
             else{
                obj.style.top=oT-2+"px";
                setTimeout(function\(\){obj.style.left=oL-2+"px"},Rate)
            }
        this.oTime=setTimeout(function\(\){om.start\(\)},speed);
     }
     this.stop=function\(\){
       clearTimeout(this.oTime);

     }
}
var nn=new SKclass(m,20,70);
&lt;/script&gt;
&lt;/body&gt;
&lt;/html&gt;

 ```