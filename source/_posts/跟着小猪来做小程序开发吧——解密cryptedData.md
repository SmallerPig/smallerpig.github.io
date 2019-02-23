---
title: 跟着小猪来做小程序开发吧——解密cryptedData
tags:
  - flask
  - wechat
  - 小程序
  - 微信
id: 1186
comment: false
categories:
  - 微信开发
date: 2017-04-11 23:51:53
---

在上一篇中，小猪介绍了微信小程序中获取用户的唯一标识的两种方式。

值得注意的是，小程序框架里没有直接将openid和unionid以明文的方式给到前端。而是需要我们的服务器去通过微信的API来得到。

所以，要想小程序能够基于我们本身的业务逻辑正常运行，服务器正确处理用户数据是最基础的工作，所以今天小猪再花一篇来介绍上篇文章中说到的第二种方式来获取用户标识。

## 为什么使用unionid

这种方式不仅相对安全，更关键的是，如果我们的程序本身不止依赖小程序这一个平台，还有本身的安卓APP，苹果APP，或者微信公众号，想要这些平台之间的用户统一，那么我们的小程序的用户标识就不能使用openid而必须使用unionid了。官方介绍是openid是对公众号与用户唯一对应的。也就是说同一个用户，在不同的公众号、小程序之间的openid是不同的。而如果多个公众号或者小程序都关联到微信开发者平台的话，那么相同的微信用户对应这些公众号、小程序、APP的unionID是相同的。只有使用unionID作为用户标识，在使用微信登录我们的本身程序时才可能将相同的微信用户识别成同一用户，达到业务逻辑跨平台的效果。

## 开始我们的程序吧

在上一篇中，我们使用python的flask框架完成了一个接口，该接口接收小程序POST过来的code，利用该code，通过API向微信请求数据，微信服务器向我们返回了类似下列格式的数据：
```
{
    "session_key":"oRCrTPgGwAf7jwdqV0g+Ig==",
    "expires_in":7200,
    "openid":"oWv370DkivlAs-LPrxKKvQ9KP98w"
}


```

如果我们只想使用openid的话，那到这里已经能够正确得到数据了。这一篇我们用到上述数据的`session_key`的值。

## session_key的时效性

根据微信小程序文档的说明：

> 通过上述接口获得的用户登录态拥有一定的时效性。用户越久未使用小程序，用户登录态越有可能失效。反之如果用户一直在使用小程序，则用户登录态一直保持有效。具体时效逻辑由微信维护，对开发者透明。开发者只需要调用wx.checkSession接口检测当前用户登录态是否有效。登录态过期后开发者可以再调用wx.login获取新的用户登录态。

小猪做了下测试，在昨天使用的代码上再次请求，请求到的session_key没有变化，也就是说这个session_key的有效时间是挺长的，具体长到什么时间，官方并没有给出一个明确的值，只是做了说明，这个值由微信服务器自己维护。不过我们为了确保程序的逻辑正常，需要在使用session_key之前先检查该session_key还是否继续可用。
下图来自官方文档，是官方建议的获取用户信息的逻辑。
![](https://mp.weixin.qq.com/debug/wxadoc/dev/image/login.png)

NOTE:
小猪写这里的代码时遇到过一个问题，就是本来想通过flask框架的session来保存session_key，但这里的问题是虽然是同一个开发工具的ajax请求，请求中却不会自动带上cookies，所以这里我们不能使用session来保存session_key。

## wx.checkSession接口

简单的示例程序

```  

wx.checkSession({
  success: function\(\){
    //session 未过期，并且在本生命周期一直有效
  },
  fail: function\(\){
    //登录态过期
    wx.login\(\) //重新登录
    ....
  }
})


```

另外可参考官方文档对这一段的说明：https://mp.weixin.qq.com/debug/wxadoc/dev/api/api-login.html#wxchecksessionobject

## wx.getUserInfo接口

[官方文档](https://mp.weixin.qq.com/debug/wxadoc/dev/api/open.html#wxgetuserinfoobject)

在拿到session_key之后，我们还要继续使用这个值。来获取用户加密的数据。
正如上一篇文章中代码所示：

```  

wx.getUserInfo({
  success: function (res) {
    that.globalData.userInfo = res.userInfo
    console.log(res)
    typeof cb == "function" &amp;&amp; cb(that.globalData.userInfo)
  }


```

上述代码的res参数，里面的`encryptedData`和`iv`字段是我们需要用到的。当然，res里面的userInfo字段里面已经有一些基础信息的数据了。
根据小程序官方文档解析数据，我们可以得到下列数据：

```  

{
  u'province': u'Beijing', 
  u'openId': u'oWv370DkivlAs-LPrxKKvQ9KP98w', 
  u'language': u'zh_CN', 
  u'city': u'', 
  u'gender': 1, 
  u'avatarUrl': u'http://wx.qlogo.cn/mmopen/vi_32/Q0j4TwGTfTJX4t68vicwIadxD7g3aYkvrX4ibasZfqmgcPeG58uf3Plw7ftv8Rr15jgIx2Xp1VhAz2OmTOIeaUCg/0', 
  u'watermark': 
  {
      u'timestamp': 1491924827, 
      u'appid': u'wx8fa41e5f33e2***'
  }, 
  u'country': u'CN', 
  u'nickName': u'\u542f'
}


```

因为小猪的测试小程序账号并没有绑定微信开放平台账号，所以上述数据中并没有unionid数据，只要绑定了相应的账号，就会出现对应数据。

完整的flask代码如下：

```  

appid = 'wx8fa41e5f33e***'
secret = '***'

@app.route('/user/getuserinfo', methods=['GET', 'POST'])
def getuserinfo\(\):
    code = request.data
    url = 'https://api.weixin.qq.com/sns/jscode2session?appid=%s&amp;secret=%s&amp;js_code=%s&amp;grant_type=authorization_code' % (appid, secret, code)
    r = requests.get(url)
    result = r.text
    j = json.loads(result)
    cache.set('xcx_session_key',j['session_key'])
    return result

@app.route('/user/getuserunionid', methods=['GET', 'POST'])
def getuserid\(\):
    r = json.loads(request.data)
    encryptedData = r['encryptedData']
    iv = r['iv']
    session_key = cache.get('xcx_session_key')
    pc = WXBizDataCrypt(appid, session_key)
    return pc.decrypt(encryptedData, iv)

if __name__ == '__main__':
    app.run(debug=True)


```

完整的小程序js代码：

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
                console.log(res)
                that.globalData.openid = res.data.openid
                console.log(res.data.openid)
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
              wx.request({
                url: 'https://***.smallerpig.com/user/getuserunionid',
                data: res,
                method: 'POST', // OPTIONS, GET, HEAD, POST, PUT, DELETE, TRACE, CONNECT
                header: {'content-type':'application/json'}, // 设置请求的 header
                success: function(res){
                  // success
                  console.log(res)
                },
                fail: function(res) {
                  // fail
                },
                complete: function(res) {
                  // complete
                }
              })
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
这样，我们就可以在《微信web开发者工具》中的控制台里看到我们需要的unionid数据了。

## 小结

本篇文章使用了官方推荐的方式来给cryptedData数据做了解密。如果你认真看完文章并且阅读代码的话你会发现小猪其实是将两次请求的session_key放在python的缓存里了。也就是单单运行本程序来单个测试的话是没有问题的，但是多个用户一起来测的话就有问题了。

另外需要说明的是：小程序的wx.request请求并不会带上cookies，也就是想直接通过flask框架自身带的session功能来辨识用户的话是行不通的。

所以正确的做法应该是在wx.login的时候保存自己业务逻辑的session到微信小程序中，然后在调用wx.getUserInfo接口时带上这个session，然后我们的业务逻辑根据这个session来正确获取session_key.这也就是下一篇文章要讲到的。

正好，也可以利用这个功能来介绍小程序的另外一个常用的功能：本地数据存储。