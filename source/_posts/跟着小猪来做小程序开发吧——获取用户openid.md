---
title: 跟着小猪来做小程序开发吧——获取用户openid
tags:
  - wechat
  - 小程序
  - 微信
id: 1184
comment: false
categories:
  - 微信开发
date: 2017-04-10 16:21:31
---

休息了一个周末之后我们今天继续来小程序的开发。

在上一篇的介绍中，小猪向大家简单介绍了微信小程序的默认demo。

其中有一段是涉及到用户的用户信息的，我们在小程序里也看到了我的头像和昵称信息。

研究下他的代码，主要在`app.js`文件中。
```
//app.js
App({
  onLaunch: function \(\) {
    //调用API从本地缓存中获取数据
    var logs = wx.getStorageSync('logs') || []
    logs.unshift(Date.now\(\))
    wx.setStorageSync('logs', logs)
  },
  getUserInfo:function(cb){
    var that = this
    if(this.globalData.userInfo){
      typeof cb == "function" &amp;&amp; cb(this.globalData.userInfo)
    }else{
      //调用登录接口
      wx.login({
        success: function \(\) {
          wx.getUserInfo({
            success: function (res) {
              that.globalData.userInfo = res.userInfo
              typeof cb == "function" &amp;&amp; cb(that.globalData.userInfo)
            }
          })
        }
      })
    }
  },
  globalData:{
    userInfo:null
  }
})


```

上述代码从15行开始涉及到调用登录接口。这里调用了小程序的api：`wx.login`。这里说明下，小程序给开发者封装好的API都是直接使用js的方法来调用，开发者直接使用类似`wx.login\(\)`的方法就可以与微信的服务器进行交互，开发者不需要知道小程序框架到底调用了哪个地址，传了哪些参数。
紧接着调用`wx.getUserInfo`接口，该接口可直接返回用户数据.具体调用方法可参考[官方文档](https://mp.weixin.qq.com/debug/wxadoc/dev/api/open.html#wxgetuserinfoobject)

为了查看返回结果，我们在代码18行位置插入两句打印日志代码，可以查看具体的结果。

```  

wx.getUserInfo({
      success: function (res) {
          console.log(res.userInfo)
          console.log(res.encryptedData)
          that.globalData.userInfo = res.userInfo


```

可以看到控制台中打印出了对应的结果。
![图片地址](http://wx2.sinaimg.cn/mw690/88e12591gy1fehnn69xyvj21kw0td10l.jpg)

## 例子

接下来的例子小猪将使用官方hello world的例子，一般在我们的程序中不会将用户的标识直接展示给用户，作为演示，我们将本来显示昵称的位置的下方显示出该小程序对应该用户的openid。

## 获取用户的openid

这里我们看到通过上述接口获取的信息都是一些比较基本的信息，包括：昵称、头像地址、性别等公开的数据。这些数据可以方便的显示在用户界面上，可是有时候我们需要系统的识别出该用户，也就是需要该用户的辨识。

在小程序的代码中，服务器端代码有两种方式来获取到用户的openID。

### 方式一，通过wx.login接口

直接调用`wx.login`接口，该接口会返回一个code字段，利用该code值可以向微信服务器开放的API请求用户的openid，例如下列flask代码：

```  

from flask import Flask, request
import requests

app = Flask(__name__)
appid = 'wx8fa41e5f33e*****'
secret = '47d4ed43a683f800e66169c09dd*****'

@app.route('/user/getuserinfo', methods=['GET', 'POST'])
def getuserinfo\(\):
    code = request.data
    print code
    url = 'https://api.weixin.qq.com/sns/jscode2session?appid=%s&amp;secret=%s&amp;js_code=%s&amp;grant_type=authorization_code' % (appid, secret, code)
    r = requests.get(url)
    result = r.text
    return result

if __name__ == '__main__':
    app.run(debug=True)


```

配合小程序的代码：

```  

//app.js
App({
  onLaunch: function \(\) {
    //调用API从本地缓存中获取数据
    var logs = wx.getStorageSync('logs') || []
    logs.unshift(Date.now\(\))
    wx.setStorageSync('logs', logs)
  },
  getUserInfo:function(cb){
    var that = this
    if(this.globalData.userInfo){
      typeof cb == "function" &amp;&amp; cb(this.globalData.userInfo)
    }else{
      //调用登录接口
      wx.login({
        success: function (r) {
          console.log(r)
              wx.request({
                url: 'https://***.smallerpig.com/user/getuserinfo',
                data: r.code,
                method: 'POST', // OPTIONS, GET, HEAD, POST, PUT, DELETE, TRACE, CONNECT
                // header: {}, // 设置请求的 header
                success: function(res){
                  console.log('success')
                  that.globalData.openid = res.data.openid
                  console.log(res.data.openid)
                  // success
                },
                fail: function(res) {
                  console.log('fail')
                  console.log(res)
                  // fail
                },
                complete: function(res) {
                  console.log('complete')
                  console.log(res)
                  // complete
                }
              })
          wx.getUserInfo({
            success: function (res) {
              that.globalData.userInfo = res.userInfo
              typeof cb == "function" &amp;&amp; cb(that.globalData.userInfo)
            }
          })
        }
      })
    }
  },
  globalData:{
    userInfo:null,
    openid:null
  }
})


```

上述js的小程序代码大致逻辑为：
在调用`wx.login`并且执行成功之后将请求到的code值post到我们自己的服务器上面，在服务器上请求微信服务器的API，微信服务器得到正确值之后会得给到openid和session_key。
如下图：
![成功获取到用户的openId](http://wx4.sinaimg.cn/mw690/88e12591gy1fehnn7j46kj21kw0vo0yv.jpg)

### 方式二，通过wx.getUserInfo接口

在之前的公众号开发时，我们可以直接使用openId来作为用户的标识，在小程序中我们同样可以使用该字段。只是该字段没有显式的表示出来，而是通过res.encryptedData里。
通过之前的截图可以看出，encryptedData是通过加密的数据，根据[官方文档](https://mp.weixin.qq.com/debug/wxadoc/dev/api/signature.html)解释，我们只要使用指定的算法来解密就可以得到我们想要的openID。

解密算法如下：

*   对称解密使用的算法为 AES-128-CBC，数据采用PKCS#7填充。
*   对称解密的目标密文为 Base64_Decode(encryptedData)。
*   对称解密秘钥 aeskey = Base64_Decode(session_key), aeskey 是16字节。
*   对称解密算法初始向量 为Base64_Decode(iv)，其中iv由数据接口返回。

官方也给出了几种语言的[demo解密程序](https://mp.weixin.qq.com/debug/wxadoc/dev/demo/aes-sample.zip)

通过这种方式不仅可以获取到用户的openid,也可以获取到用户的unionId。

### 两种方式比较

方式一可以直接获取到了用户的openid,但是没办法获取到unionId，如果咱们程序需要用到unionId的话就没有办法，肯定得用第二种方法了，如果只是为了获取到openId那可以使用方式一来获取。

不过方式二相比方式一相对复杂，需要通过第一步获取到的session_key，再解密数据。也就是想要使用第二种方式的话其实第一种方法也得走一遍。

最后，从安全性来将当然方式二更安全一点。

* * *

下面附上小程序的index.js和index.wxml：

```
//index.js
//获取应用实例
var app = getApp\(\)
Page({
  data: {
    motto: 'Hello World',
    userInfo: {},
    openid:null
  },
  //事件处理函数
  bindViewTap: function\(\) {
    wx.navigateTo({
      url: '../logs/logs'
    })
  },
  onLoad: function \(\) {
    console.log('onLoad')
    var that = this
    //调用应用实例的方法获取全局数据
    app.getUserInfo(function(userInfo){
      //更新数据
      that.setData({
        userInfo:userInfo,
        openid:app.globalData.openid
      })
    })
  }
})

<!--index.wxml-->
<view class="container">
  <view  bindtap="bindViewTap" class="userinfo">
    <image class="userinfo-avatar" src="{{userInfo.avatarUrl}}" background-size="cover"></image>
    <text class="userinfo-nickname">{{userInfo.nickName}}</text>
    <text class="userinfo-nickname">{{openid}}</text>
  </view>
  <view class="usermotto">
    <text class="user-motto">{{motto}}</text>
  </view>
</view>
```
## 结语

这一篇介绍了获取用户openid的两种方式，并使用第一种方式结合flask框架写了一个demo程序。
其中还用到了小程序API当中的一个非常重要的接口，就是`wx.request`。也就是通过该接口向我们自身的业务服务器请求数据，有点类似js中的ajax的作用，从《微信web开发者工具》的调试界面也可以看出其实际上就是通过xhr的方式来请求我们的业务服务器。

另外需要说明的是微信的小程序本身禁止使用ajax来请求网络资源。

下一篇小猪将重点介绍使用方式二来解密用户的数据，服务器端代码同样使用flask来实现。