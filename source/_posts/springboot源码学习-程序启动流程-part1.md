---
title: springboot源码学习-程序启动流程-part1
tags:
  - springboot
categories:
  - java
date: 2018-12-05 15:13:24
id: 20181205
---

要想用好springboot，不仅仅是知道怎么样用，而且要知道为啥这么用，他是怎么实现这么用的。。

计划写一些列阅读springboot源码的文章，写不写的完再说，反正有时间的话就多看看。

今天就以启动springboot开始来这一系列吧。

<!-- more -->

本文基于springboot-1.5.X系列

首先写把整体流程抛出来：

>在`context`的`refresh`方法中，需要注册`bean definition`，实例化`bean`.在加载`bean defintion`的时候使用`ConfigurationClassParser`类来解析我们的主类。然后在解析主类的时候发现了`@EnableAutoConfiguratio`注解中的`@Import`注解，就去处理`@Import`注解中的`value`值，然后就使用`ImportSelector`来获取被配置在`spring.factories`中的类。这些类通常是`AutoConfiguration`。这些`configuration`中包含了各种各样的`bean`

一般我们的springboot程序都会有个核心的类：
``` java
@SpringBootApplication
public class ZhuqiStudyPureSpringbootApplication {

	public static void main(String[] args) {
		SpringApplication.run(ZhuqiStudyPureSpringbootApplication.class, args);
    System.out.println("hello world");
	}
}
```


这里在main函数里调用了SpringApplication的静态函数run()方法。
到SpringApplication类里看看究竟发生了什么，可以看到如下的调用：
``` java

public static ConfigurableApplicationContext run(Object source, String... args) {
  return run(new Object[] { source }, args);
}

public static ConfigurableApplicationContext run(Object[] sources, String[] args) {
  return new SpringApplication(sources).run(args);
}
```

## 实例化SpringApplication, 通过调用initialize方法

这里调用了`SpringApplication`的构造函数，然后执行run方法，构造函数和run方法分别为：
``` java

public SpringApplication(Object... sources) {
  initialize(sources);
}

@SuppressWarnings({ "unchecked", "rawtypes" })
private void initialize(Object[] sources) {
  if (sources != null && sources.length > 0) {
    this.sources.addAll(Arrays.asList(sources));
  }
  this.webEnvironment = deduceWebEnvironment();
  setInitializers((Collection) getSpringFactoriesInstances(
      ApplicationContextInitializer.class));
  setListeners((Collection) getSpringFactoriesInstances(ApplicationListener.class));
  this.mainApplicationClass = deduceMainApplicationClass();
}


public void setInitializers(
    Collection<? extends ApplicationContextInitializer<?>> initializers) {
  this.initializers = new ArrayList<ApplicationContextInitializer<?>>();
  this.initializers.addAll(initializers);
}

public void setListeners(Collection<? extends ApplicationListener<?>> listeners) {
  this.listeners = new ArrayList<ApplicationListener<?>>();
  this.listeners.addAll(listeners);
}
```

### 先设置 webEnvironment
`webEnvironment`变量是一个`boolean`类型的变量，通过名字猜测是用来判断是否`application`是否为web程序，设置方法为：
``` java
private static final String[] WEB_ENVIRONMENT_CLASSES = { "javax.servlet.Servlet",
    "org.springframework.web.context.ConfigurableWebApplicationContext" };

private boolean deduceWebEnvironment() {
  for (String className : WEB_ENVIRONMENT_CLASSES) {
    if (!ClassUtils.isPresent(className, null)) {
      return false;
    }
  }
  return true;
}
```

### 再来设置成员变量Initializers
`initialize`方法的代码中还调用了`getSpringFactoriesInstances(ApplicationContextInitializer.class)`，来获取`ApplicationContextInitializer`类型对象的列表。

``` java
private <T> Collection<? extends T> getSpringFactoriesInstances(Class<T> type) {
  return getSpringFactoriesInstances(type, new Class<?>[] {});
}

private <T> Collection<? extends T> getSpringFactoriesInstances(Class<T> type,
    Class<?>[] parameterTypes, Object... args) {
  ClassLoader classLoader = Thread.currentThread().getContextClassLoader();
  // Use names and ensure unique to protect against duplicates
  Set<String> names = new LinkedHashSet<String>(
      SpringFactoriesLoader.loadFactoryNames(type, classLoader));
  List<T> instances = createSpringFactoriesInstances(type, parameterTypes,
      classLoader, args, names);
  AnnotationAwareOrderComparator.sort(instances);
  return instances;
}
```
在该方法中，首先通过调用`SpringFactoriesLoader.loadFactoryNames(type, classLoader)`来获取所有`Spring Factories`的名字，然后调用`createSpringFactoriesInstances`方法根据读取到的名字创建对象。最后会将创建好的对象列表排序并返回。

``` java
public static List<String> loadFactoryNames(Class<?> factoryClass, ClassLoader classLoader) {
  String factoryClassName = factoryClass.getName();
  try {
    Enumeration<URL> urls = (classLoader != null ? classLoader.getResources(FACTORIES_RESOURCE_LOCATION) :
        ClassLoader.getSystemResources(FACTORIES_RESOURCE_LOCATION));
    List<String> result = new ArrayList<String>();
    while (urls.hasMoreElements()) {
      URL url = urls.nextElement();
      Properties properties = PropertiesLoaderUtils.loadProperties(new UrlResource(url));
      String factoryClassNames = properties.getProperty(factoryClassName);
      result.addAll(Arrays.asList(StringUtils.commaDelimitedListToStringArray(factoryClassNames)));
    }
    return result;
  }
  catch (IOException ex) {
    throw new IllegalArgumentException("Unable to load [" + factoryClass.getName() +
        "] factories from location [" + FACTORIES_RESOURCE_LOCATION + "]", ex);
  }
}
```

可以看到，是从一个名字叫META-INF/spring.factories的资源文件中，读取key为org.springframework.context.ApplicationContextInitializer的value。而spring.factories的部分内容如下：

``` factories
# Application Context Initializers
org.springframework.context.ApplicationContextInitializer=\
org.springframework.boot.context.ConfigurationWarningsApplicationContextInitializer,\
org.springframework.boot.context.ContextIdApplicationContextInitializer,\
org.springframework.boot.context.config.DelegatingApplicationContextInitializer,\
org.springframework.boot.context.embedded.ServerPortInfoApplicationContextInitializer
```

可以看到，最近的得到的，是
- ConfigurationWarningsApplicationContextInitializer,
- ContextIdApplicationContextInitializer，
- DelegatingApplicationContextInitializer，
- ServerPortInfoApplicationContextInitializer
这四个类的名字。

接下来会调用createSpringFactoriesInstances来创建ApplicationContextInitializer实例。

``` java
@SuppressWarnings("unchecked")
private <T> List<T> createSpringFactoriesInstances(Class<T> type,
    Class<?>[] parameterTypes, ClassLoader classLoader, Object[] args,
    Set<String> names) {
  List<T> instances = new ArrayList<T>(names.size());
  for (String name : names) {
    try {
      Class<?> instanceClass = ClassUtils.forName(name, classLoader);
      Assert.isAssignable(type, instanceClass);
      Constructor<?> constructor = instanceClass
          .getDeclaredConstructor(parameterTypes);
      T instance = (T) BeanUtils.instantiateClass(constructor, args);
      instances.add(instance);
    }
    catch (Throwable ex) {
      throw new IllegalArgumentException(
          "Cannot instantiate " + type + " : " + name, ex);
    }
  }
  return instances;
}

```

所以在我们的例子中，SpringApplication对象的成员变量initalizers就被初始化为，
- ConfigurationWarningsApplicationContextInitializer，
- ContextIdApplicationContextInitializer，
- DelegatingApplicationContextInitializer，
- ServerPortInfoApplicationContextInitializer
这四个类的对象组成的list。

下图画出了加载的ApplicationContextInitializer，并说明了他们的作用。

![](https://ws1.sinaimg.cn/large/006tNbRwly1fxw17ra9h8j30m30dcjsj.jpg)

### 接下来是成员变量listeners
listeners成员变量，是一个ApplicationListener<?>类型对象的集合。可以看到获取该成员变量内容使用的是跟成员变量initializers一样的方法，只不过传入的类型从ApplicationContextInitializer.class变成了ApplicationListener.class。


同样也可以从META-INF/spring.factories中找到对应的定义，
```
# Application Listeners
org.springframework.context.ApplicationListener=\
org.springframework.boot.ClearCachesApplicationListener,\
org.springframework.boot.builder.ParentContextCloserApplicationListener,\
org.springframework.boot.context.FileEncodingApplicationListener,\
org.springframework.boot.context.config.AnsiOutputApplicationListener,\
org.springframework.boot.context.config.ConfigFileApplicationListener,\
org.springframework.boot.context.config.DelegatingApplicationListener,\
org.springframework.boot.liquibase.LiquibaseServiceLocatorApplicationListener,\
org.springframework.boot.logging.ClasspathLoggingApplicationListener,\
org.springframework.boot.logging.LoggingApplicationListener
```


也就是说，在我们的例子中，listener最终会被初始化为
- ClearCachesApplicationListener,
- ParentContextCloserApplicationListener，
- FileEncodingApplicationListener，
- AnsiOutputApplicationListener，
- ConfigFileApplicationListener，
- DelegatingApplicationListener，
- LiquibaseServiceLocatorApplicationListener，
- ClasspathLoggingApplicationListener，
- LoggingApplicationListener
这几个类的对象组成的list。

下图画出了加载的ApplicationListener，并说明了他们的作用。
![](https://ws2.sinaimg.cn/large/006tNbRwly1fxw1lbje5bj30uk0fp0v2.jpg)

### 最后是mainApplicationClass
``` java
private Class<?> deduceMainApplicationClass() {
  try {
    StackTraceElement[] stackTrace = new RuntimeException().getStackTrace();
    for (StackTraceElement stackTraceElement : stackTrace) {
      if ("main".equals(stackTraceElement.getMethodName())) {
        return Class.forName(stackTraceElement.getClassName());
      }
    }
  }
  catch (ClassNotFoundException ex) {
    // Swallow and continue
  }
  return null;
}
```
在deduceMainApplicationClass方法中，通过获取当前调用栈，找到入口方法main所在的类，并将其复制给SpringApplication对象的成员变量mainApplicationClass。在我们的例子中mainApplicationClass即是我们自己编写的Application类:`ZhuqiStudyPureSpringbootApplication`。


那么接下来就是执行run方法了。

这一部分我们留到下面的part2吧。