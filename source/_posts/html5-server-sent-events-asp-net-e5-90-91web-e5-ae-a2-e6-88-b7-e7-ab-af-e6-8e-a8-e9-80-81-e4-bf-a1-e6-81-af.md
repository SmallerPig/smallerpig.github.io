---
title: HTML5 Server-sent Events ASP.NET向Web客户端推送信息
id: 302
categories:
  - ASP.NET
  - 'C#'
  - HTML
  - JS
  - MVC
  - WEBFORM
  - WEB开发
date: 2013-09-11 10:42:17
tags:
---

**前言**

在 Web 应用中，浏览器和服务器之间使用的是请求 / 响应的交互模式。浏览器发出请求，服务器根据收到的请求来生成相应的响应。浏览器再对收到的响应进行处理，展现给用户。响应的格式可能是 HTML、XML 或 JSON 等。随着 REST 架构风格和 AJAX 的流行，服务器更多地使用 JSON 作为响应的数据格式。Web 应用使用 XMLHttpRequest 对象来发送请求，并根据服务器端返回的数据，对页面的内容进行动态更新。通常来说，用户在页面上的操作，比如点击或移动鼠标，会触发相应的事件。由 XMLHttpRequest 对象来发出请求，得到服务器响应之后进行页面的局部更新。这种方式的不足之处在于：服务器端产生的数据变化不能及时地通知浏览器，而是需要等到下次请求发出时才能被浏览器获取。对于某些对数据实时性要求很高的应用来说，这种延迟是不能接受的。
为了满足这类应用的需求，就需要有某种方式能够从服务器端推送数据给浏览器，以保证服务器端的数据变化可以在第一时间通知给用户。目前常见的解决办法有不少，主要可以分成两类。这两类方法的区别在于是否基于 HTTP 协议来实现。不使用 HTTP 协议的做法是使用 HTML 5 新增的 WebSocket 规范，而使用 HTTP 协议的做法则包括简易轮询、COMET 技术和本文中要介绍的 HTML 5 服务器推送事件。

* * *

**规范**
Server-sent Events 规范是 HTML 5 规范的一个组成部分，具体的规范文档见参考资源。该规范比较简单，主要由两个部分组成：第一个部分是服务器端与浏览器端之间的通讯协议，第二部分则是在浏览器端可供 JavaScript 使用的 EventSource 对象。通讯协议是基于纯文本的简单协议。服务器端的响应的内容类型是“text/event-stream”。响应文本的内容可以看成是一个事件流，由不同的事件所组成。每个事件由类型和数据两部分组成，同时每个事件可以有一个可选的标识符。不同事件的内容之间通过仅包含回车符和换行符的空行（“rn”）来分隔。每个事件的数据可能由多行组成。代码清单 1 给出了服务器端响应的示例。

清单一：
```
data: first event

data: second event
id: 100

event: myevent
data: third event
id: 101

: this is a comment
data: fourth event
data: fourth event continue
 ```

如代码清单 1 所示，每个事件之间通过空行来分隔。对于每一行来说，冒号（“:”）前面表示的是该行的类型，冒号后面则是对应的值。可能的类型包括：

*   类型为空白，表示该行是注释，会在处理时被忽略。

*   类型为 data，表示该行包含的是数据。以 data 开头的行可以出现多次。所有这些行都是该事件的数据。

*   类型为 event，表示该行用来声明事件的类型。浏览器在收到数据时，会产生对应类型的事件。

*   类型为 id，表示该行用来声明事件的标识符。

*   类型为 retry，表示该行用来声明浏览器在连接断开之后进行再次连接之前的等待时间。

在代码清单 1 中，第一个事件只包含数据“first event”，会产生默认的事件；第二个事件的标识符是 100，数据为“second event”；第三个事件会产生类型为“myevent”的事件；最后一个事件的数据为“fourth eventnfourth event continue”。当有多行数据时，实际的数据由每行数据以换行符连接而成。
如果服务器端返回的数据中包含了事件的标识符，浏览器会记录最近一次接收到的事件的标识符。如果与服务器端的连接中断，当浏览器端再次进行连接时，会通过 HTTP 头“Last-Event-ID”来声明最后一次接收到的事件的标识符。服务器端可以通过浏览器端发送的事件标识符来确定从哪个事件开始来继续连接。
对于服务器端返回的响应，浏览器端需要在 JavaScript 中使用 EventSource 对象来进行处理。EventSource 使用的是标准的事件监听器方式，只需要在对象上添加相应的事件处理方法即可。EventSource 提供了三个标准事件(open, message, error )，

如之前所述，服务器端可以返回自定义类型的事件。对于这些事件，可以使用 addEventListener 方法来添加相应的事件处理方法。代码清单 2 给出了 EventSource 对象的使用示例。

清单 2\. EventSource 对象的使用示例
```
vares = newEventSource('events');
es.onmessage = function(e) {
    console.log(e.data);
};

es.addEventListener('myevent', function(e) {
    console.log(e.data);
});
 ```

如代码清单 2 所示，在指定 URL 创建出 EventSource 对象之后，可以通过 onmessage 和 addEventListener 方法来添加事件处理方法。当服务器端有新的事件产生，相应的事件处理方法会被调用。EventSource 对象的 onmessage 属性的作用类似于 addEventListener( ‘ message ’ )，不过 onmessage 属性只支持一个事件处理方法。
在介绍完服务器推送事件的规范内容之后，下面介绍服务器端的实现。

* * *

**服务器端和浏览器端实现**

从上一节中对通讯协议的描述可以看出，服务器端推送事件是一个比较简单的协议。服务器端的实现也相对比较简单，只需要按照协议规定的格式，返回响应内容即可。在开源社区可以找到各种不同的服务器端技术相对应的实现。自己开发的难度也不大。

代码清单3：服务器端对应代码

```
publicvoidEvents\(\)
{
    Response.ContentType = "text/event-stream";
    Response.Expires = -1;
    while(Response.IsClientConnected)
    {
        //Response.Write("event: add");
        Response.Write(string.Format("event: testeventndata: {0}nn", DateTime.Now.ToString\(\)));
        Response.Flush\(\);
        Thread.Sleep(1000);
    }
    Response.Close\(\);
}
 ```

如代码清单3所示：在ASP.NET MVC中实现，该代码在对应的Controller中定义一个方法，定期返回服务器时间。如果不定义事件类型则默认事件名为：message。当客户端持续链接未断开时可以看到控制台在输出对应的信息。

参考链接：http://www.ibm.com/developerworks/cn/web/1307_chengfu_serversentevent/index.html

该文章中使用了java作为后台语言实现同样的推送功能。