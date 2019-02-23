---
title: hystrix的最大并发量设置
tags:
  - spring cloud
  - hystix
categories:
  - java
date: 2018-07-10 09:03:20
id: what-hystrix-maxConcurrentRequest-meaning
---



## 问题

组内同学在开发过程中遇到了个问题，
在使用`@HystrixCommand(fallbackMethod = "fallBackVisistInfo")`注解对某个负载均衡的请求方法进行操作时，在大并发请求下，总是会有大部分的请求直接进入fallback，根本不会去请求真正对应的微服务。

如下代码：
<!-- more -->

``` java
@Override
@HystrixCommand(fallbackMethod = "fallBackVisistInfo")
public JSONObject getUserVisitInfo(String userkey ) {
    String url = "http://serverid/api/ping?"
            +"userKey=" +userkey ;
    logger.info("请求链接     {}",url );
    JSONObject result = restTemplate.getForObject(url ,JSONObject.class);
    logger.info("request result :{}", result.toJSONString());
    return result;
}

public JSONObject fallBackVisistInfo(String username) {
    logger.debug("【请求异常】，使用默认值{},{}",0,0);
    JSONObject jsonObject  =  new JSONObject();
    jsonObject.put("returnCode", RequestConstants.REQUEST_SUCCESS_CODE);
    jsonObject.put("returnMsg", RequestConstants.REQUEST_SUCCESS_MSG);
    jsonObject.put("returnData", new JSONObject());
    return  jsonObject;
}
```
当然，定位到该问题需要个过程，今天分享的是怎样解决这个问题。

## 解决过程
从google中找到了这样的一个问题：
https://stackoverflow.com/questions/39609636/hystrixruntimeexception-testcommand-fallback-execution-rejected

> I am using Hystrix 1.5.5 version. When I do load testing of bigger load like 1000 thread/second, all the requests are going through fallback method. Meanwhile, I am getting below exception too. Why do I get this below exception. Test Command is my custom Hystrix class

大概意思就是在使用1000个请求/秒的情况下，会有大量的请求直接进入了FALLBACK，
而下面的回复也正是我想要的：

> You need to check on the maxConcurrentRequests value for both execution & fallback. Below url discusses on this issue.
> https://github.com/Netflix/Hystrix/issues/796

意思是你需要设置`execution` 和 `fallback` 的 `maxConcurrentRequests`值，跳转到GitHub上面看看
看到github的issue上的讨论：
> As described in the Wiki (https://github.com/Netflix/Hystrix/wiki/How-it-Works#semaphores), Hystrix uses a semaphore on the number of concurrent fallbacks that may be in flight at any point in time. It must do this so that latency on the fallback path is able to be controlled, especially since fallbacks may run on the calling thread.
> Your options for not encountering this situtation:
> - Trigger the fallback path less frequently (fail the command less often)
> - Make the fallback path less expensive/latent
> - Reconfigure the fallback semaphore : https://github.com/Netflix/Hystrix/wiki/Configuration#fallback.isolation.semaphore.maxConcurrentRequests (Note that this may affect the resiliency/stability of your system if you increase this too much)

正如上面的回复，
默认的Hystrix会限制某个请求的最大并发量：默认10，如果超过了这个默认的并发值且开启了fallback，则丢弃剩下的请求直接进入fallback方法，找到方法之后就是修改配置的事情了。
不过作者也建议谨慎修改该值，该值的合理性会影响生产环境的稳定性

在配置文件中加上如下配置：
```
hystrix.command.default.execution.isolation.strategy=SEMAPHORE
# 核心的两个设置，允许并发量1000的请求，默认情况下下面两个值都是10，也就是超过10个的并发会直接进入fallback方法，不会去真正请求
hystrix.command.default.execution.isolation.semaphore.maxConcurrentRequests=1000 
hystrix.command.default.fallback.isolation.semaphore.maxConcurrentRequests=1000

hystrix.command.default.execution.timeout.enabled=false 
hystrix.command.default.circuitBreaker.enabled=false
hystrix.command.default.fallback.enabled=true
```
上述代码将允许的最大并发请求设置为1000，不过在生产环境下还是需要谨慎设置该值，


另外需要读者好好了解的是hystrix的两种策略
也就是配置项`hystrix.command.default.execution.isolation.strategy`的两个值
### THREAD 默认
运行在独立的线程里，并发请求量受线程池数量限制


### SEMAPHORE
并发请求量受 semaphore设置量大小。

在设置为`SEMAPHORE`之后可以设置maxConcurrentRequests的数量了。

关于上述两个关键值，官方文档的解释是：
#### execution.isolation.semaphore.maxConcurrentRequests
This property sets the maximum number of requests allowed to a HystrixCommand.run() method when you are using ExecutionIsolationStrategy.SEMAPHORE.

If this maximum concurrent limit is hit then subsequent requests will be rejected.

The logic that you use when you size a semaphore is basically the same as when you choose how many threads to add to a thread-pool, but the overhead for a semaphore is far smaller and typically the executions are far faster (sub-millisecond), otherwise you would be using threads.

For example, 5000rps on a single instance for in-memory lookups with metrics being gathered has been seen to work with a semaphore of only 2.

The isolation principle is still the same so the semaphore should still be a small percentage of the overall container (i.e. Tomcat) thread pool, not all of or most of it, otherwise it provides no protection.



#### fallback.isolation.semaphore.maxConcurrentRequests
This property sets the maximum number of requests a HystrixCommand.getFallback() method is allowed to make from the calling thread.

If the maximum concurrent limit is hit then subsequent requests will be rejected and an exception thrown since no fallback could be retrieved.



设置好值之后就可以解决