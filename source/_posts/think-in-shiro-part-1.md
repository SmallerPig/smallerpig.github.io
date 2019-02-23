---
title: 在SpringCloud中使用shiro - par1 思考
tags:
  - shiro
categories:
  - java
  - springboot
  - springcloud
date: 2018-09-04 11:40:41
id: thinking-in-shiro-part1
---


在传统的架构中直接使用shiro还是比较方便的，虽然shiro是使用各种接口来调用，但是都默认提供了实现：
例如
```
DefaultWebSecurityManager=>DefaultSecurityManager=>SecurityManager=>SessionManager
```
所以我们甚至都不需要太了解shiro的内部结构，直接简单复制网上的几个配置就可以拿来使用了。正所谓开箱即用。

但是在遇到复杂的应用场景的时候就黔驴技穷了，需要自己深入的研究下shiro的实现方式。

能够百度到的文章千篇一律，最有价值的还是要数 老前辈2014的《跟我学shiro系列》文章了：http://jinnianshilongnian.iteye.com/blog/2018398。


建议如果需要深入学习shiro的话可以学习下作者的文章。

但毕竟是2014年的博客了，对于最近几年的更新换代，尤其是SpringCloud等微服务系列的出现，文章中并没有给出实践，这里就来写写我在SpringCloud中使用shiro的思考。

<!-- more -->

在知乎专栏上见到过这么一篇文章：https://zhuanlan.zhihu.com/p/29345083

>用户权限
>传统的单体应用的权限拦截，大家都喜欢shiro，而且用的颇为顺手。可是一旦拆分后，这权限开始分散在各个API了，shiro还好使吗？笔者在项目中，并没有用shiro。前后端分离后，交互都是token，后端的服务无状态化，前端按钮资源化，权限放哪儿管好使？

对于题主的问题，我是不同意他的观点的。首页他的文章中的架构每一个微服务都进行了鉴权，而我觉得鉴权是应该统一来做，而正在的服务是可以不用管权限的。所有的用户请求都走网关程序，只要在网关侧做好权限的控制的话就不存在作者说的权限放哪管好使不好使的问题。

网上另外的文章就是不是使用shiro来实现微服务架构的权限控制的，典型的如：
http://blueskykong.com/2017/10/19/security1/


本系列文章不比较spring security 和 shiro的优劣，假定读者已经选型shiro作为认证鉴权的框架。结合spring cloud的微服务来做整个系统的搭建。

## zuul
遇到的第一个问题就是在微服务架构中的网关的集成问题，很多微服务的文章中多介绍网关的一大职责就是鉴权、所以需要在网关中集成鉴权模块，目前zuul作为springcloud官方推荐的组件，势必能够很好的集成shiro。

可惜的是并没有现成的例子，网上能找到的资料也比较少。百度的就更少了，稍微提及到的是纯洁的微笑的文章：springcloud(十一)：服务网关Zuul高级篇：http://www.ityouknow.com/springcloud/2018/01/20/spring-cloud-zuul.html

zuul本身提供了zuulfilter来拦截所有请求，shiro也提供了拦截器链。这样具体的拦截请求到底是放在哪呢？个人觉得还是放在shiro的`filterChainDefinitionMap`比较好。让组件只做自己的事，让zuul拦截器只做网关级别的拦截。

## auth是放在网关还是单独部署一个服务
我觉得此问题要看具体的项目来，如果项目足够简单，直接放在网关里，网关集成鉴权的所有功能是可以的，然后在shiro 的`shiroFilterFactoryBean`里定义好所有的url对应的规则。

不过这样的话就会大量的使用filter来过滤请求，不好使用具体的申明式注解`@RequirePermission()`来完成权限的控制。

另外，采用此方法需要在网关里定义好所有的url所对应的规则，例如下列的代码：
```
@Bean(name = "shiroFilter")
public ShiroFilterFactoryBean shiroFilterFactoryBean(SecurityManager securityManager) {
    ShiroFilterFactoryBean shiroFilterFactoryBean = new ShiroFilterFactoryBean();
    shiroFilterFactoryBean.setSecurityManager(securityManager);
    Map<String, Filter> filterMap = new LinkedHashMap<>();
    filterMap.put("authc", new AuthorityFilter());
    filterMap.put("roles", new CustomeRolesAuthorizationFilter());
    shiroFilterFactoryBean.setFilters(filterMap);
    Map<String, String> filterChainDefinitionMap = new LinkedHashMap<>();
    filterChainDefinitionMap.put("/application/**", "roles[admin,user]");
    filterChainDefinitionMap.put("/**", "anon");
    shiroFilterFactoryBean.setFilterChainDefinitionMap(filterChainDefinitionMap);
    return shiroFilterFactoryBean;
}
```
通常上述代码就定义了调用`application`开头的接口必须要有admin或者role的权限。
用类似方法可以实现所有功能。只不过这种hard code的方式个人觉得不是特别的妥。


另外就是放在单独的微服务里，这样提高了拓展性，但是程序的架构上更加复杂。


其他部分抽出来第二部分再继续讨论吧。