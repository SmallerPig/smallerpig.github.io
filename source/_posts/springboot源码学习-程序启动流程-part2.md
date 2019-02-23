---
title: springboot源码学习-程序启动流程-part2
tags:
  - springboot
categories:
  - java
date: 2018-12-05 22:53:35
id: 20181206
---


还记得上一篇的开始嘛，向大家介绍了springboot程序启动的整个过程：

>在`context`的`refresh`方法中，需要注册`bean definition`，实例化`bean`.在加载`bean defintion`的时候使用`ConfigurationClassParser`类来解析我们的主类。然后在解析主类的时候发现了`@EnableAutoConfiguratio`注解中的`@Import`注解，就去处理`@Import`注解中的`value`值，然后就使用`ImportSelector`来获取被配置在`spring.factories`中的类。这些类通常是`AutoConfiguration`。这些`configuration`中包含了各种各样的`bean`

可是上一篇整个过程都还没进入到上述的内容呢，都是在为下面的流程做准备，直到目前为止，都是在做准备工作，屏幕上能看到的是啥也没有，一起来进入这一篇的学习吧。
<!-- more -->

本篇重点是看下返回`ConfigurableApplicationContext`的run方法。

``` java
public ConfigurableApplicationContext run(String... args) {
  StopWatch stopWatch = new StopWatch();
  stopWatch.start();//开始计时
  ConfigurableApplicationContext context = null;
  FailureAnalyzers analyzers = null;
  configureHeadlessProperty(); //设置headless模式
  SpringApplicationRunListeners listeners = getRunListeners(args);
  listeners.starting();// 启动监听器
  try {
    ApplicationArguments applicationArguments = new DefaultApplicationArguments(
        args);
    ConfigurableEnvironment environment = prepareEnvironment(listeners,
        applicationArguments);
    Banner printedBanner = printBanner(environment); // banner对象
    context = createApplicationContext();
    analyzers = new FailureAnalyzers(context);
    prepareContext(context, environment, listeners, applicationArguments,
        printedBanner);
    refreshContext(context);
    afterRefresh(context, applicationArguments);
    listeners.finished(context, null);
    stopWatch.stop();//结束计时
    if (this.logStartupInfo) {
      new StartupInfoLogger(this.mainApplicationClass)
          .logStarted(getApplicationLog(), stopWatch);
    }
    return context;
  }
  catch (Throwable ex) {
    handleRunFailure(context, listeners, analyzers, ex);
    throw new IllegalStateException(ex);
  }
}
```

args 就是我们main方法传过来的参数
run函数首先启动了计时器来统计程序的启动时间，然后配置headless模式(headless模式可另行参考其他文章)。

### 启动计时器
``` java
StopWatch stopWatch = new StopWatch();
stopWatch.start();//开始计时
```

### 配置headless模式
``` java
// configureHeadlessProperty(); //设置headless模式

private static final String SYSTEM_PROPERTY_JAVA_AWT_HEADLESS = "java.awt.headless";

private void configureHeadlessProperty() {
  System.setProperty(SYSTEM_PROPERTY_JAVA_AWT_HEADLESS, System.getProperty(
      SYSTEM_PROPERTY_JAVA_AWT_HEADLESS, Boolean.toString(this.headless)));
}
```

### 创建了应用的监听器SpringApplicationRunListeners并开始监听

``` java
// SpringApplicationRunListeners listeners = getRunListeners(args);
// listeners.starting();// 启动监听器

private SpringApplicationRunListeners getRunListeners(String[] args) {
  Class<?>[] types = new Class<?>[] { SpringApplication.class, String[].class };
  return new SpringApplicationRunListeners(logger, getSpringFactoriesInstances(
      SpringApplicationRunListener.class, types, this, args));
}
```

### 加载SpringBoot配置环境(ConfigurableEnvironment)

如果是通过web容器发布，会加载StandardEnvironment，其最终也是继承了ConfigurableEnvironment，类图如下

![](https://ws3.sinaimg.cn/large/006tNbRwgy1fxx2ri5joij30q20m6402.jpg)
可以看出，*Environment最终都实现了PropertyResolver接口，我们平时通过environment对象获取配置文件中指定Key对应的value方法时，就是调用了propertyResolver接口的getProperty方法

然后配置环境(Environment)加入到监听器对象中(SpringApplicationRunListeners)

### 创建run方法的返回对象： ConfigurableApplicationContext(应用配置上下文), 通过方法`createApplicationContext`创建：

```java
protected ConfigurableApplicationContext createApplicationContext() {
  Class<?> contextClass = this.applicationContextClass;
  if (contextClass == null) {
    try {
      contextClass = Class.forName(this.webEnvironment
          ? DEFAULT_WEB_CONTEXT_CLASS : DEFAULT_CONTEXT_CLASS);
    }
    catch (ClassNotFoundException ex) {
      throw new IllegalStateException(
          "Unable create a default ApplicationContext, "
              + "please specify an ApplicationContextClass",
          ex);
    }
  }
  return (ConfigurableApplicationContext) BeanUtils.instantiate(contextClass);
}
```
方法会先获取显式设置的应用上下文(applicationContextClass)，如果不存在，再加载默认的环境配置（通过是否是web environment判断），默认选择AnnotationConfigApplicationContext注解上下文（通过扫描所有注解类来加载bean），最后通过BeanUtils实例化上下文对象，并返回，ConfigurableApplicationContext类图如下：
![](https://ws3.sinaimg.cn/large/006tNbRwgy1fxx3fm0axrj31hk0cawg5.jpg)

主要看其继承的两个方向：

LifeCycle：生命周期类，定义了start启动、stop结束、isRunning是否运行中等生命周期空值方法

ApplicationContext：应用上下文类，其主要继承了beanFactory(bean的工厂类)

### prepareContext方法将listeners、environment、applicationArguments、banner等重要组件与上下文对象关联
``` java
Banner printedBanner = printBanner(environment); // banner对象
context = createApplicationContext();
analyzers = new FailureAnalyzers(context);
prepareContext(context, environment, listeners, applicationArguments,
    printedBanner);
```

准备好了之后就可以进入最重要的部分：
### refreshContext(context)
refreshContext(context)方法(初始化方法如下)将是实现spring-boot-starter-*(mybatis、redis等)自动化配置的关键，包括spring.factories的加载，bean的实例化等核心工作。
``` java
private void refreshContext(ConfigurableApplicationContext context) {
  refresh(context);
  if (this.registerShutdownHook) {
    try {
      context.registerShutdownHook();
    }
    catch (AccessControlException ex) {
      // Not allowed in some environments.
    }
  }
}

protected void refresh(ApplicationContext applicationContext) {
  Assert.isInstanceOf(AbstractApplicationContext.class, applicationContext);
  ((AbstractApplicationContext) applicationContext).refresh();
}
```
下面代码为抽象类`AbstractApplicationContext`的refresh方法代码：
``` java
@Override
public void refresh() throws BeansException, IllegalStateException {
  synchronized (this.startupShutdownMonitor) {
    // Prepare this context for refreshing.
    prepareRefresh();

    // Tell the subclass to refresh the internal bean factory.
    ConfigurableListableBeanFactory beanFactory = obtainFreshBeanFactory();

    // Prepare the bean factory for use in this context.
    prepareBeanFactory(beanFactory);

    try {
      // Allows post-processing of the bean factory in context subclasses.
      postProcessBeanFactory(beanFactory);

      // Invoke factory processors registered as beans in the context.
      invokeBeanFactoryPostProcessors(beanFactory);

      // Register bean processors that intercept bean creation.
      registerBeanPostProcessors(beanFactory);

      // Initialize message source for this context.
      initMessageSource();

      // Initialize event multicaster for this context.
      initApplicationEventMulticaster();

      // Initialize other special beans in specific context subclasses.
      onRefresh();

      // Check for listener beans and register them.
      registerListeners();

      // Instantiate all remaining (non-lazy-init) singletons.
      finishBeanFactoryInitialization(beanFactory);

      // Last step: publish corresponding event.
      finishRefresh();
    }
    ...
  }
}
```

配置结束后，Springboot做了一些基本的收尾工作，返回了应用环境上下文。
``` java
afterRefresh(context, applicationArguments);
listeners.finished(context, null);
stopWatch.stop();
...
```

## 总结
回顾整体流程，Springboot的启动，主要创建了配置环境(environment)、事件监听(listeners)、应用上下文(applicationContext)，并基于以上条件，在容器中开始实例化我们需要的Bean，至此，通过SpringBoot启动的程序已经构造完成


到这里，加载就完成了，最核心的部分就是refresh(context)的部分了，其中包括sprig-boot-*-start系列的自动配置部分。

下面一篇来学习学习spring-boot是如何实现读取application.properties文件或者application.yml文件的配置，而且在某些配置不存在时是如何给他们附上默认值的。
