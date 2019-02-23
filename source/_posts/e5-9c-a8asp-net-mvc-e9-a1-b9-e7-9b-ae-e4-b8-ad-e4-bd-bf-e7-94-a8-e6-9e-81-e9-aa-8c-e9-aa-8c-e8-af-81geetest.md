---
title: 在ASP.NET MVC项目中使用极验验证(geetest)
tags:
  - 极验
id: 979
categories:
  - ASP.NET
  - MVC
  - WEB开发
date: 2016-03-02 18:22:37
---

我们一般在做项目时为了保证提交的数据是真实用户数据而非机器人提交一般都会添加验证码操作来让用户在提交表单的同时填上服务端生成的验证码，这样在一定程度上能够防止暴力提交或者暴力破解。但是却在一定程度上增加了开发的成本。

现在的互联网上各种产品都能够独立开发使用，今天小猪就为大家介绍[极验验证](http://www.geetest.com/)这个工具。
目前网络中已经可以发现大量使用该产品的应用，用户只需简单的滑动滑块来进行验证([体验地址](http://www.geetest.com/exp_normal))，一定程度上提高了用户体验。

极验官方提供了C#版本的服务端[SDK](http://www.geetest.com/install/sections/idx-server-sdk.html#csharp)。并且提供了一个简单的[DEMO](https://github.com/GeeTeam/gt-csharp-sdk)。可惜该DEMO是Web Form版本的。而目前大家使用的比较多的是ASP.NET MVC项目，所以今天小猪就在其官方DEMO的基础上改成了ASP.NET MVC项目。

*   新建ASP.NET MVC项目，并在项目中引用C#版本的SDK。
*   申请极验账号并添加验证。如图：
![极验后台添加验证](http://ww1.sinaimg.cn/large/88e12591gw1f1io9e7f96j20qz0ivwj0.jpg)
成功添加后我们可以看到对应的ID和KEY。</p>
*   新建测试控制器 AccountController

*   在控制器中随意添加一个Action，例如Login
*   在对应的View里添加代码（例如Login.cshtml）：
```
<div id="geetest-container">

</div>
<script src="http://static.geetest.com/static/tools/gt.js"></script>

<script>
    window.addEventListener('load',processGeeTest);

    function processGeeTest\(\) {
        $.ajax({
            // 获取id，challenge，success（是否启用failback）
            url: "/Account/GeekTest",
            type: "get",
            dataType: "json", // 使用jsonp格式
            success: function (data) {
                // 使用initGeetest接口
                // 参数1：配置参数，与创建Geetest实例时接受的参数一致
                // 参数2：回调，回调的第一个参数验证码对象，之后可以使用它做appendTo之类的事件
                initGeetest({
                    gt: data.gt,
                    challenge: data.challenge,
                    product: "float", // 产品形式
                    offline: !data.success
                }, handler);
            }
        });
    }

    var handler = function (captchaObj) {
        // 将验证码加到id为captcha的元素里
        captchaObj.appendTo("#geetest-container");

        captchaObj.onSuccess = function(e) {
            console.log(e);
        }

    };
</script>
```

上述代码中使用了JQuery库，需要同时引用。
其中我们使用Ajax来动态请求后台AccountController的GeeTest方法，代码如下：

```  
public ActionResult GeekTest\(\)
    {
        string result = getCaptcha\(\);

        return Content(result, "application/json");
    }

    private String getCaptcha\(\)
    {
        GeetestLib geetest = new GeetestLib(geetest_publicKey, geetest_privateKey);
        Byte gtServerStatus = geetest.preProcess\(\);
        Session[GeetestLib.gtServerStatusSessionKey] = gtServerStatus;
        return geetest.getResponseStr\(\);
    }

 ```

上述代码中的`geetest_publicKey`和`geetest_privateKey`分别对应极验后台申请到的ID和KEY。

这样我们就可以运行程序看到效果。接下来的工作就是确认用户在提交对应的表单的时候有没有通过极验的验证了。

在接受表单提交的POST地址里面输入代码，例如：

```  

 //
// POST: /Account/Login
[HttpPost]
[AllowAnonymous]
[ValidateAntiForgeryToken]
public async Task<ActionResult> Login(LoginViewModel model, string returnUrl)
{
    if (!CheckGeeTestResult\(\))
    {
        ModelState.AddModelError("", "请先完成验证操作。");
        return View(model);
    }
    if (!ModelState.IsValid)
    {
        return View(model);
    }

    //其他验证逻辑
    ...
    }
}

private bool CheckGeeTestResult\(\)
{
    GeetestLib geetest = new GeetestLib(geetest_publicKey, geetest_privateKey);
    Byte gt_server_status_code = (Byte)Session[GeetestLib.gtServerStatusSessionKey];
    String userID = (String)Session["userID"];

    int result = 0;
    String challenge = Request.Form.Get(GeetestLib.fnGeetestChallenge);
    String validate = Request.Form.Get(GeetestLib.fnGeetestValidate);
    String seccode = Request.Form.Get(GeetestLib.fnGeetestSeccode);
    if (gt_server_status_code == 1) result = geetest.enhencedValidateRequest(challenge, validate, seccode, userID);
    else result = geetest.failbackValidateRequest(challenge, validate, seccode);
    if (result != 1) return false;

    return true;
}
```

上述代码中大部分引用了极验官方DEMO的代码。
这样我们在前台view中可以使用

```  
@Html.ValidationSummary(true, "", new { @class = "text-danger" })
```

来提示用户首先需要通过滑块验证才能继续操作。

这样我们就完成了将极验验证集成到我们的ASP.NET MVC项目中。并且可以在极验的后台中查看具体的数据报表：
![极验后台报表](http://ww1.sinaimg.cn/large/88e12591gw1f1ioqpc0pnj2154090t9v.jpg)

整个产品的使用我们并没有编写太多的代码，而且将复杂的验证过程交给了第三方，而我们自己只需要关心自己的业务逻辑。