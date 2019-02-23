---
title: 微信支付官方SDK v3 .NET版的坑
tags:
  - wechat
id: 720
categories:
  - 微信开发
date: 2015-05-28 11:05:44
---

我觉得玩微信支付最大的难点和瓶颈并不是微信支付本身,而是能够拿到微信支付的权限.首先微信支付所面向的开发对象不是个人,所以个人开发者不会有这样的权限,另外一方面公司的微信号又不会随便给个人进行开发,这样就陷入了一个比较尴尬的循环!

在好不容易搞到权限后,发现官方的sdk里面竟然有.NET版本,这让小猪欣喜如狂,赶紧下下来研究一番.这也就有了本文.

在设置好开发环境,测试白名单,,回调...确定微信后台设置已经没有问题之后.

接下来看.NET版本中的坑

默认Default.aspx中的链接竟然都是链接到[http://paysdk.weixin.qq.com/example/ProductPage.aspx](http://paysdk.weixin.qq.com/example/ProductPage.aspx "http://paysdk.weixin.qq.com/example/ProductPage.aspx")…这个不注意的真的会郁闷好久.将其改成我们自己的服务器测试地址.

默认他给你开启了代理服务器,你不知道的话也会坑你好久
```
//=======【代理服务器设置】===================================
/* 默认IP和端口号分别为0.0.0.0和0，此时不开启代理（如有需要才设置）
*/
public const string PROXY_URL = "http://10.152.18.220:8080";
```
如果这边不填的话,又报错,无效的 URI: 此 URI 为空。

如果填成0.0.0.0:0,报错,无效的 URI: URI 方案无效。

心都碎了.只好找到他使用代理的地方手动注释掉:

[![image](http://www.smallerpig.com/wp-content/uploads/2015/05/image_thumb5.png "image")](http://www.smallerpig.com/wp-content/uploads/2015/05/image5.png)

终于可以看到支付页面了

我们不需要使用公用地址的功能,所以在后台注释掉相关代码
```
//获取收货地址js函数入口参数
//wxEditAddrParam = jsApiPay.GetEditAddressParameters\(\);
```
把前台aspx页面的使用地址的代码也删掉.

,可是点一下支付按钮..

[![Screenshot_2015-05-28-10-03-53](http://www.smallerpig.com/wp-content/uploads/2015/05/Screenshot_2015-05-28-10-03-53_thumb.png "Screenshot_2015-05-28-10-03-53")](http://www.smallerpig.om/wp-content/uploads/2015/05/Screenshot_2015-05-28-10-03-53.png)

仔细一看,这里又特么跳转到他们自己的测试服务器上面去啦.!
```
        protected void Button1_Click(object sender, EventArgs e)
        {
            string total_fee = "1";
            if(ViewState["openid"] != null)
            {
                string openid = ViewState["openid"].ToString\(\);
                string url = "http://paysdk.weixin.qq.com/example/JsApiPayPage.aspx?openid=" + openid + "&amp;total_fee=" + total_fee;
                Response.Redirect(url);
            }
            else
            {
                Response.Write("页面缺少参数，请返回重试" + "");
                Button1.Visible = false;
                Button2.Visible = false;
                Label1.Visible = false;
                Label2.Visible = false;
            }
        }
```
改成我们自己的服务器地址后又是下单失败,只好找到代码出错的地方把try catch注释掉看看哪出错了!

[![image](http://www.smallerpig.com/wp-content/uploads/2015/05/image_thumb6.png "image")](http://www.smallerpig.com/wp-content/uploads/2015/05/image6.png)

发现还是无效的 URI: 尼玛还有地方请求设置了代理,找到注释掉:

[![image](http://www.smallerpig.com/wp-content/uploads/2015/05/image_thumb7.png "image")](http://www.smallerpig.com/wp-content/uploads/2015/05/image7.png)

这时候再打开终于见到可以看到预支付的界面了:

[![image](http://www.smallerpig.com/wp-content/uploads/2015/05/image_thumb8.png "image")](http://www.smallerpig.com/wp-content/uploads/2015/05/image8.png)

点击支付按钮也可以成功支付:

[![image](http://www.smallerpig.com/wp-content/uploads/2015/05/image_thumb9.png "image")](http://www.smallerpig.com/wp-content/uploads/2015/05/image9.png)

但是支付成功后却不能正确的执行支付结果js回调函数.看看其页面的点击事件是放在asp:Button上面的.我们知道在asp.net webform中,按钮的点击是有页面回调后台的.也就是其实点击了之后页面是有刷新的,所以这边要是想用官方的js回调的话就不能使用asp.net的服务器控件了!将点击支付的按钮改成

``` 
<button style="width:210px; height:50px; border-radius: 15px;background-color:#FE6714; border:0px #FE6714 solid; cursor: pointer;  color:white;  font-size:16px;" type="button" onclick="callpay\(\)" >立即支付</button>
```
&nbsp;

这样,我们不管在支付成功和失败后都会执行我们在界面里定义好的回调函数!

总结:不知道大TX的ASP.NET工程师是怎么想的,能够打包成sdk供用户参考必然是好的,但是却挖了这么多坑在这,想必如果没有一点基础的话对付这玩意还真挺吃力的.