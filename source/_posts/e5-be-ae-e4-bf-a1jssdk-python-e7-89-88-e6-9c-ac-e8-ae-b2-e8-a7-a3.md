---
title: 微信JSSDK python版本讲解
tags:
  - python
id: 889
categories:
  - 微信开发
date: 2015-09-16 13:50:04
---

本文对微信官方的JSSDK的python版中的代码进行讲解,并在最后给到了其对应的python3.*版本.

官方示例代码下载链接:[http://demo.open.weixin.qq.com/jssdk/sample.zip](http://demo.open.weixin.qq.com/jssdk/sample.zip "http://demo.open.weixin.qq.com/jssdk/sample.zip")

官方示例代码python版:
``` 

 import time
import random
import string
import hashlib

class Sign:
    def __init__(self, jsapi_ticket, url):
        self.ret = {
            'nonceStr': self.__create_nonce_str\(\),
            'jsapi_ticket': jsapi_ticket,
            'timestamp': self.__create_timestamp\(\),
            'url': url
        }

    def __create_nonce_str(self):
        return ''.join(random.choice(string.ascii_letters + string.digits) for _ in range(15))

    def __create_timestamp(self):
        return int(time.time\(\))

    def sign(self):
        string = '&amp;'.join(['%s=%s' % (key.lower\(\), self.ret[key]) for key in sorted(self.ret)])
        print string
        self.ret['signature'] = hashlib.sha1(string).hexdigest\(\)
        return self.ret

if __name__ == '__main__':
    # 注意 URL 一定要动态获取，不能 hardcode
    sign = Sign('jsapi_ticket', 'http://example.com')
    print sign.sign\(\)



 ```
可见代码其实非常少而且都挺简单.并没有什么高深的技术,只用到了4个模块的内容.time模块用来获取时间戳,random模块来生成随机字符串.string模块生成字符串,hashlib对数据进行sha1加密.

代码中只定义了一个类Sign,用来提供签名方法.在该类的构造函数中需要传入jsapi_ticket和url两个参数.利用这两个参数生成一个字典:ret.字典有4个键,分别为:

*   nonceStr
*   jsapi_ticket
*   timestamp
*   url
其中jsapi_ticket和url就是直接传进来的参数.时间戳调用了私有方法__create_timestamp\(\),这个方法返回当前时间戳.nonceStr调用了私有方法__create_nonce_str\(\).在__create_nonce_str\(\)方法中生成了随机字符串.

然后就是Sign类对外提供的签名方法sign\(\),在该方法中首先对ret字典进行排序并使用&amp;串联.然后对他进行sha1加密,然后将结果赋值给ret字典的signature键.最后返回ret字典.至此,整个生成验证过程结束.

其中判断__name__ 属性的代码:
``` 

 if __name__ == '__main__':
    # 注意 URL 一定要动态获取，不能 hardcode
    sign = Sign('jsapi_ticket', 'http://example.com')
    print sign.sign\(\)


 ```
作用为如果是在命令行条件下执行的话则执行这段代码,如果是其他环境下则不执行这段代码.