---
title: 在SpringCloud中使用shiro - par2
tags:
  - shiro
categories:
  - java
  - springboot
  - springcloud
date: 2018-09-04 15:33:09
id: thinking-in-shiro-part2
---


上面一篇文章简单的罗列的几个常见的问题，对于实际遇到的问题还没有给出能够运行级别的代码。

在开始这篇之前先来了解下shiro的执行顺序

<!-- more -->

1. 首先调用Subject.login(token)进行登录，其会自动委托给Security Manager，调用之前必须通过SecurityUtils. setSecurityManager()设置； 
2. SecurityManager负责真正的身份验证逻辑；它会委托给Authenticator进行身份验证； 
3. Authenticator才是真正的身份验证者，Shiro API中核心的身份认证入口点，此处可以自定义插入自己的实现； 
4. Authenticator可能会委托给相应的AuthenticationStrategy进行多Realm身份验证，默认ModularRealmAuthenticator会调用AuthenticationStrategy进行多Realm身份验证； 
5. Authenticator 会把相应的token 传入Realm，从Realm 获取身份验证信息，如果没有返回/抛出异常表示身份验证失败了。此处可以配置多个Realm，将按照相应的顺序及策略进行访问。 

然后是时候拿出官网的结构图了。
![](https://ws2.sinaimg.cn/large/006tNbRwgy1fuyif7wos6j30dn0713z4.jpg)

整个shiro的内部结构都是围绕上图来完成的。
而且可拓展性相当高，理论上可以定制任何我们想要的需求，例如下面的：


## 放弃使用subject

传统应用中可以使用session的方式来保存与客户端的对话，客户端的每次请求都带上sessionId,服务器端缓存session与subject的链接关系，查找到对应的sessionId之后找出subject就可以当做相同的用户。

但在无状态的环境里一般都是使用token的方式来鉴权。

在查找了很长时间的资料之后发现现网还没有能够直接拿来使用的demo。

比较有参考意义的是这篇帖子：https://stackoverflow.com/questions/8501058/shiro-authentication-with-sessionid-or-usernamepassword
上面帖子提出了问题就是是否每次都带上sessionId，以及如果客户端不发送sessionId而是使用对sessionId通过一定计算的方法来发送的话该怎么办。

不过投票最高的答案貌似是不推荐这么做。

以及下面的demo：
https://github.com/jikechenhao/springmvc-shiro-react-redux-restful-example
上面的demo的核心代码是每次请求都将之前的token失效，生成新的token，这似乎不太适合微服务的场景。
### 那怎么办？
我的做法是放弃使用subject，而是通过filter自己实现权限的过滤。

在filterChainDefinitionMap中设置好对应的url对应的filter以及所对应的权限要求。例如:
``` java
filterChainDefinitionMap.put("/zhuqi-ic-application/**", "roles[admin,user]");
filterChainDefinitionMap.put("/need-roles-guest/**","roles[guest]");
filterChainDefinitionMap.put("/**", "anon");
```
然后在对应的filter中直接对权限进行控制：
``` java
@Override
protected boolean isAccessAllowed(ServletRequest request, ServletResponse response, Object mappedValue) throws Exception {

    logger.trace("the isAccessAllowed is begins");

    HttpServletRequest httpServletRequest = (HttpServletRequest) request;
    String authorization = httpServletRequest.getHeader("Authorization");
    HttpServletResponse resp = (HttpServletResponse) response;

    String username = JWTUtil.getUsername(authorization);//get username from jwt token

    String[] rolesArray = (String[]) mappedValue;

    if (logger.isTraceEnabled() && rolesArray != null){
        for (String val : (String[])mappedValue){
            logger.trace("need role : {}", val);
        }
    }

    // 没有角色限制，有权限访问
    if (rolesArray == null || rolesArray.length == 0) {
        return true;
    }

    //get the roles from redis or other datasources
    Set<String> roles = getRoles(username); 
    for (String aRolesArray : rolesArray) {
        // 若当前用户是rolesArray中的任何一个，则有权限访问
        if (roles.contains(aRolesArray)) {
            logger.trace("the isAccessAllowed result is true!!");
            return true;
        }
    }
    logger.trace("the isAccessAllowed result is false!!");
    return false;
}
```
需要做的是从数据源拿出来token所对应的user所对应的roles，似乎有点绕口。但你能明白的。当然，适当的缓存还是需要的。


### 放弃subject之后会有什么问题？
其实这个时候我们就放弃了使用subject的一系列优点，典型的是不能使用类似官网上面很方便的通用api了。

``` java
//role
if ( currentUser.hasRole( "schwartz" ) ) {
    log.info("May the Schwartz be with you!" );
} else {
    log.info( "Hello, mere mortal." );
}

// permitted
if ( currentUser.isPermitted( "lightsaber:weild" ) ) {
    log.info("You may use a lightsaber ring.  Use it wisely.");
} else {
    log.info("Sorry, lightsaber rings are for schwartz masters only.");
}


currentUser.logout(); //清除识别信息，设置 session 失效.
```
但是我们假定的场景不就是无状态的么？

是不是？

另外编写单元测试代码时也无疑增加了难度，本来可以通过runas来解决作为某个角色来登录的，结果这么一搞貌似复杂了。
