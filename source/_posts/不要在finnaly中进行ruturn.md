---
title: 不要在finnaly中进行ruturn
tags:
  - java
categories:
  - java
date: 2018-12-03 15:53:33
id: 20181203
---

阿里巴巴开发手册中的异常处理部分有这么一条：
>不要在finally块中使用return
>说明：finally块中的return返回后方法结束执行，不会再执行try块中的return语句。

很多同学不知道这一条的具体意义，今天来通过实际代码来说明：

<!-- more -->

看下示例代码：
``` java
public class notreturninfinally {

    public static void main(String[] args){
        int i= 1;
        int j =0;
        try {
            System.out.println("try -");
            divide(i, j);
        }catch (Exception e){
            System.out.println("catch -------");
        }finally {
            System.out.println("finally -------");
        }
    }

    static int divide(int i, int j){
        try {
            System.out.println("divide try -------");
            int r = i / j;
        }catch (Exception e){
            System.out.println("divide catch --------");
            throw e;
        }finally {
            System.out.println("divide  finally---------");
            return 0;
        }
    }
}
```
上述代码的`divide`方法中的finally代码中有一句`return 0;`，当我们执行这段代码时看看会发生什么。

```
![](https://ws3.sinaimg.cn/large/006tNbRwly1fxtm6ljf1jj30ni0803z4.jpg)
```
图中可见在`divide`方法抛出异常之后进入了`finally`，在`finally`里进行了返回之后进入到了调用者的`finally`里，而这里本来应该进入调用者的`catch`里的流程被这里破坏了。所以最终的输出里没见到`catch -------`的输出。

这也就是为啥我们不能在finally里执行return语句的原因。

在实际的代码过程中调用者是希望能够获取到调用的异常信息的，如果在调用的finally里进行了返回，那即使在对应的catch里也抛出了异常，调用者也不会catch到。


