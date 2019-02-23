---
title: 自动清理OA待办事项
tags:
  - python
categories:
  - 小猪的感悟
date: 2018-09-18 17:10:00
id: complete-todo
---


## 事情
事情是这样的，上周人事公布了公司的待办事项排名，我竟然榜上有名，紧接着人事竟然发布新政要求以后必须要及时清理oa的待办事项，如果在整个公司的排名是后100位不能参加绩效评a/s.


虽然我觉得这事本身没必要，我也是不愿意平时工作的时候被各种通知分心的，而如果不得不为这种事情分神的话也太不值得了。

所以我做了如下工作：

<!-- more -->

而因为抓这事大家也心知肚明的很多的待办大家都不看的，最多浏览下标题直接就点“结束”了。

最直接的原因是我不喜欢信息是“推”(push)给我的，而我喜欢的是“拉”（pull）信息。这也是我不得不把很多微信群甚至APP的推送给屏蔽的原因。毕竟我喜欢主动。。。。哈哈。。。。。

我需要真正的break things是不得不立刻放下手下的事去处理，像这种随时可能来的信息就让程序自动去跑去吧。

有了整个过程之后就差分析下方法了。

## 准备

待办可以通过APP来阅读，阅读完了可以点击已阅结束来完成这个事项。
而OA的APP也很简单，通过待办的列表进入到具体的待办项，待办项里有已阅按钮。

既然有APP的话那一定是有服务端的api接口的，开发人员不会把接口给到我的。所以只能“偷偷的”看看他们的APP在我手机里做了什么了，毕竟我看我自己手机的内容他们不知道吧？

而我需要做的事情就是用程序自动来帮我们“点”已阅按钮而已。

## 抓包
首先我们要知道获取待办事项的url，这时候该拿出“小花瓶(Charles)”了，windows下可以用fiddler。设置好代理并设置手机的网络都走小花瓶的代理之后就可以抓包了。
![](https://ws1.sinaimg.cn/large/006tNbRwgy1fvcjop5n5dj30x60ts0y2.jpg)
手机上打开OA的APP试试，
好多请求呢。
![](https://ws4.sinaimg.cn/large/006tNbRwgy1fvcjoa26cjj31kw13k7pt.jpg)

## 分析
app打开的时候要请求这么多url，合理性我们先不管了，看看待办的请求吧。
手机上打开待办列表，发现请求待办数据的url
![](https://ws2.sinaimg.cn/large/006tNbRwgy1fvcjprufxej31kw14mkgw.jpg)

发现url上面没有版本信息呢，那说明以后即使APP更新了，只要url地址没办那就好办。
同时发现url上面也没有用户的信息，且请求为POST类型，那要想知道是哪个用户的请求那可能为body里，也可能在header里。我们来看看请求头，再看看请求body:

![](https://ws2.sinaimg.cn/large/006tNbRwgy1fvcjw9erl9j31kw12uamy.jpg)

请求body：
``` json
{
	"userName": "zhuqi1",
	"mobileNum": "1377101XXXX",
	"pageSize": 10,
	"pageNum": 1,
	"appSessionId": "SNI47E05941-BA2D-11E8-AA9C-179C84BE354D"
}

```

果然有个字段是token。猜的没错的话这个就是鉴权信息了，我们把这个信息保留。

同样为了伪造请求，我们把User-Agent 的值也保存好。毕竟很多服务会利用User-Agent来判断请求来源类型，如果是浏览器的话可能直接被拒绝。

剩下的就是appSessionId这个值的来源了。难道是每次打开APP的时候都向服务器请求一个值，那这个值跟token的意义就又点重复了。

那这个值具体是用来干啥的呢，随便改成一个其他值试试看请求会成功么，
![](https://ws2.sinaimg.cn/large/006tNbRwgy1fvckc6hb2qj31ig0laqeb.jpg)

如上图，第一次请求的是完整的复制curl请求，下面的为我改动了sessionID的值之后请求，发现请求是失败的，那说明这个值还不能乱动呢，服务器端是对这个值有判断的呢，直接复制好了。如果他确实是服务器用来判断回话状态的依据的话那说明服务器端程序有可能是没有设计成无状态登录的，那么我通过轮询的方式来请求的话这个sessionID理论上也不会失效。所以先尝试试试呗。

接下来就是进入到具体的待办项里面了。

关于待办无非就是先要找到这个待办信息的标识，然后在模拟请求结束的请求中带上这个待办标识。
1. 到具体的待办事项信息里就去标识。
2. 查看是如何去请求“已阅结束的”。

那么，接着怎么办？

等公司人力一姐给我们指派待办啊，因为之前的已经都处理好了，只能等新的了，经过了一整天的等待，终于来了。打开待办列表，请求如下：

![](https://ws3.sinaimg.cn/large/006tNbRwgy1fvdncbooiyj31kw0xiqiq.jpg)

我们重点关注下返回的几个ID，包括了：
1. boInsId
2. documentId
3. taskId

而我猜测最重要的就是第三个了，当有一个公司的整体待办时，生成一个documentId,当部门人力指派到部门的所有员工时，再为这个documentId和每一个员工生成一个对应的taskId。

我再大胆猜测下如果当时人力一姐的操作比较快的话，那么生成的列表中的taskId两个之间间隔的数字就应该是我们部门的人数-1，减去的一个就是人力一姐自己了。我们来试一下：
```
2284315 - 2282789 = 1526
2282789 - 2281809 = 980
```
啊哦,尴尬，看来不是的。难道是我中间点掉过一个？不纠结这个了，应该是平台的程序不是我猜测的这么生成taskId的。

然后进入最重要的环节，就是模拟请求了。拿一个真实的请求看看是什么样子。

![](https://ws3.sinaimg.cn/large/006tNbRwgy1fvdns4ytdzj31kw0xi13f.jpg)

url可以看到为saveAndSubmit，请求的参数里面重点为BoInsId和taskId,（这里吐槽下开发人员，啥请求，命名不规则啊，第一个字母一会大写一会小写的）

我去，点击了之后又生成了一个activityId是什么鬼，可以发现请求列表里面也生成了一个请求的url为finalSubmit。看一下：
![](https://ws4.sinaimg.cn/large/006tNbRwgy1fvdnv76yzxj31kw0xigve.jpg)

这里应该就是整个流程都结束了。

整理下整个过程：
1. 我们通过url地址：todoList来请求所有的待办信息
2. 通过列表返回的信息我们可以拿到待办的boInsId,documentId(没用),taskId.
3. 通过boInsId和taskId请求url:saveAndSubmit能够拿到activityId。
4. 再使用activityId请求finalSubmit来结束整个待办。

这其中第三步和第四步是由app自动完成的，我想其中应该是因为本身设计的流程应该是还有其他流程，例如人力一姐的流程一般都是要在第三步之后做其他事情而不是直接就结束了，所以还会有其他的流程来处理这个待办，但作为员工的我们只需要阅读就可以了，所以直接结束就OK了。


## 动手

分析差不多了之后就差动手写代码了，这里我使用的是Python，java一样的，推荐使用springboot,里面可以使用定时器来执行代码。

首先是需要配置的地方：
``` python
userName = 'xxx' # 用户名
mobileNum = 'xxx' # 手机号
appSessionId = 'SNI9CEA1975-BAD8-11E8-AA9C-179C84Bxxxx' # app的sessionid
token = 'inIbq6Gq7k88N1iqFH0gLQ3Y794fIQ22EGb+SqtDfD+wKEHveiIm2MA3wWWgkLES5WE1wmUqEzxxxxxxxxxx' # app的token

useAgent = 'Iot_Portal_App/1 CFNetwork/974.2.1 Darwin/18.0.0'
baseURL = 'http://183.230.40.146:7080/'
todoListURL = baseURL + 'portal/newmobile/document/todoList'
saveAndSubmitURL = baseURL + 'portal/newmobile/document/saveAndSubmit'
finalSubmitURL = baseURL + 'portal/newmobile/document/finalSubmit'

# 存放待办列表数据
todoList = []
````
然后封装好统一的请求头：
``` python
# 公用的请求头
headers = {
    'Host': '183.230.40.146:7080',
    'User-Agent': useAgent,
    'Content-Type': 'application/json',
    'Connection': 'keep-alive',
    'timeout': '120000',
    'Accept': 'application/json',
    'Accept-Language': 'zh-cn',
    'token': token,
    'Accept-Encoding': 'gzip,deflate'
}
```
获取代表列表数据：
``` python
# 获取待办列表
def get_todo():
    body = {
        "userName": userName,
        "mobileNum": mobileNum,
        "pageSize": 10,
        "pageNum": 1,
        "appSessionId": appSessionId
    }
    r = requests.post(todoListURL, data=json.dumps(body), headers=headers)
    return r.json()['list']

```


提交、并获取该任务的activityId


``` python
# 提交、并获取该任务的activityId
def save_and_submit(boInsId, taskId):
    body = {
        "userName": userName,
        "mobileNum": mobileNum,
        "BoInsId": boInsId,
        "YHPath": "结束",
        "YHBtnName": "已阅结束",
        "needOpinion": True,
        "taskId": taskId,
        "appSessionId": appSessionId
    }
    r = requests.post(saveAndSubmitURL, data=json.dumps(body), headers=headers)
    return r.json()['activityId']

```

最终提交结束流程
```python
# 最终提交结束流程
def final_submit(activityId, boInsId, taskId):
    body = {
        "userName": userName,
        "mobileNum": mobileNum,
        "activityId": activityId,
        "BoInsId": boInsId,
        "selectUserId": -1,
        "taskId": taskId,
        "appSessionId": appSessionId
    }
    r = requests.post(finalSubmitURL, data=json.dumps(body), headers=headers)
    return r.json()

```

写个main函数跑起来：
``` python
if __name__ == '__main__':
    todoList = get_todo()
    if len(todoList) > 0:
        # 遍历待办列表数据
        for item in todoList:
            print(item["boInsId"], item["taskId"])
            activityId = save_and_submit(item["boInsId"], item["taskId"])
            print("get taskId : %s" % (activityId, ))
            result = final_submit(activityId, item["boInsId"], item["taskId"])
            if result["success"]:
                print("the todo item complete success .data: boInsId: %s, taskId : %s , title: %s , lastUser: %s " %
                      (item["boInsId"], item["taskId"], item["title"], item["lastUser"]))
            # 失败了
            else:
                print("something wrong!! : %s" % (result, ))
            # 低调点，完成一个待办项之后10秒之后再进入下一个待办清空。
            sleep(10)
    print todoList

```

试着跑一下代码：

![](https://ws4.sinaimg.cn/large/006tNbRwgy1fvdrhmiyuqj31kw0ky108.jpg)

可以从控制台上面看到请求完成了，打开手机APP的待办列表，果然被清空了。
只是想再调试调试的时候已经没有数据提供给调试了，哈哈。

所以代码就到这一步吧。

剩下最后一步了，那就是启动个线程持续跑，想了想规则还是设置一下，每分钟跑一次吧，为了让自己的脚本发起的请求不像一个脚本发起的，我们生成一个随机的时间间隔吧。

``` python
if __name__ == '__main__':

    while True:
        …………
        # 随机一分钟到两分钟之间跑一次
        sleep(random.randint(60,120))
```

再跑一次，简直完美：
![](https://ws4.sinaimg.cn/large/006tNbRwgy1fvdsy3ui24j31kw0lrn5u.jpg)

## 总结说明
可以从代码中看出来很多地方都是hard code的，但这个小脚本只是自己使用，能跑起来完成目的就可以了，等哪天服务端的API调整了之后代码也就用不了了，不过思路总归是这个思路，可能稍微修改下就又可以使用了。

另外代码也不能够提供给其他使用者使用，毕竟要使用的话需要知道自己的appsessionId和token，这些数据在不抓包APP的情况下是无法获得的，因为app使用了“本机号码一键登录”功能，没有登录接口。

纯为学习使用，不为代码的任何细节负责。
