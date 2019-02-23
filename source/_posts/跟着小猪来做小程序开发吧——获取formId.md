---
title: 跟着小猪来做小程序开发吧——获取formId
tags:
  - wechat
  - 小程序
  - 微信
id: 1192
comment: false
categories:
  - 微信开发
date: 2017-04-17 11:06:30
---

接下来的几篇小猪将围绕模板消息推动的功能分别向读者介绍小程序的几个功能

今天要介绍的是获取`formId`

## 为什么要获取formId

在回答这个问题之前先简单的介绍下微信的模板消息功能。

在一开始，模板消息这个权限可不是所有都公众号都有的，即使你已经交了300块完成了微信认证。只有一些比较牛逼的企业例如说各个银行，人家的需求是类似在每次有交易信息的时候通知用户，这样的功能简直不能太好，人家本来是通过短信通知的，现在通过微信就可以了，而且还不收费，走运营商的短信接口可是需要大概一毛一条的。

所以这个权限是只有走内部流程的。

大概是在13年底的时候微信向所有认证的服务号开放了申请接口。这下模板消息这个功能就彻底开放出来了，每一个公众号都可以发送自己的模板消息，用户会在该公众号的回话窗口看到模板消息。

可是小程序是没有单独的回话窗口的，要使用模板消息，那用户收到的模板消息会在哪呢？

答案是在微信的单独的一个“服务通知”回话框里。

可是如果每一个小程序都可以像公众号那样无限制的发送模板消息的话，那这个“服务通知”的会话框很快就会被占领。

所以必须有一定的限制小程序使用模板消息，微信目前的限制是在如下两种情况下小程序才能够正常的使用模板消息：
1\. 在小程序内使用了微信支付接口，
2\. 在小程序里用户点击了表单，而且该表单的report-submit属性值为true时。

在上述第二种情况下，小程序框架会给出一个formid，利用这个formId才能正常发送模板消息。

## 怎么获取formId？

使用类似下面的代码，注意表单的report-submit属性。
```
// demo.wxml
<form bindsubmit="formSubmit" bindreset="formReset" report-submit = "true">

  <view class="section section_gap">
    <view class="section__title">checkbox</view>
    <checkbox-group name="checkbox">
      <label><checkbox value="checkbox1"/>checkbox1</label>
      <label><checkbox value="checkbox2"/>checkbox2</label>
    </checkbox-group>
  </view>
  <view class="btn-area">
    <button formType="submit">Submit</button>
    <button formType="reset">Reset</button>
  </view>
</form>


```

然后在对应的js文件里加上：

```  

Page({
  formSubmit: function(e) {
    console.log('form发生了submit事件，fromId为：', e.detail.formId)
  },
  formReset: function\(\) {
    console.log('form发生了reset事件')
  },
  onLoad:function\(\){
      console.log('sform load')
  }
})
```

单机提交按钮之后可以看到运行结果：

> form发生了submit事件，携带数据为： the formId is a mock one

因为我们是在IDE中测试，所以得到的formId值为`the formId is a mock one`。在真机中我们可以得到一个具体的值，利用该值结合其他参数我们就可以发送模板消息啦。

下一篇小猪将使用flask结合mysql数据库来做一个demo，把这个formId 存储起来供下次使用。