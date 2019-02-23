---
title: springboot源码学习-自动配置
tags:
  - springboot
categories:
  - java
date: 2018-12-24 21:52:02
id: 20181224
---

今天平安夜，本来以为会是不醉不归的一晚，却跟兄弟们简单吃了点，回来之后通过写几行代码来平静心情。这些天经历了很多事情，心态有个很大的起伏，这期间得到了一些也失去了一些，这些都不重要了，心安即是归处。做好眼前的事情吧，很多年以后回头来看都只是一些经历，人还是要向前看的，你们说是么？

说好的一切还得继续，太阳明天还会照常升起（虽然最近一直阴天）,自己只是在庸人自扰罢了！

![](https://ws2.sinaimg.cn/large/006tNbRwgy1fyi7nvrjqvj30dw08o3yu.jpg)

还是来继续看看spring-boot的源码吧。

<!-- more -->

## 自动配置 auto-configuration

在查看注解`SpringBootApplication`的源码时会发现这个注解上面有开启自动配置`EnableAutoConfiguration`
``` java
@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Inherited
@SpringBootConfiguration
@EnableAutoConfiguration
@ComponentScan(excludeFilters = {
		@Filter(type = FilterType.CUSTOM, classes = TypeExcludeFilter.class),
		@Filter(type = FilterType.CUSTOM, classes = AutoConfigurationExcludeFilter.class) })
public @interface SpringBootApplication {
...

```

启用了``EnableAutoConfiguration`注解之后，springboot 会自动”猜“你想要做哪些事情，例如如果是web程序，他会猜你需要启动tomcat、然后启动监听，如果你引入了MysalDataSource，那就猜需要连接mysql数据库，并且默认的使用localhost:3306的连接信息来尝试连接。

那springboot怎么知道我们在哪些时候加载配置呢？答案就在Condtion系列注解

## Condition
Condition系列注解包含了一系列判断条件，例如ConditionalOnClass，意思是告诉程序在只有存在某类时该配置才会生效。
其中包含:

@ConditionalOnClass和@ConditionalOnMissingClass
ConditionalOnBean和ConditionalOnMissingBean
@ConditionalOnProperty
@ConditionalOnResource
@ConditionalOnWebApplication和@ConditionalOnNotWebApplication
@ConditionalOnExpression注释允许基于一个的结果被包括配置[使用SpEL表达](https://docs.spring.io/spring/docs/4.3.9.RELEASE/spring-framework-reference/htmlsingle/#expressions)


当然使用最多的就是ConditionalOnClass和ConditionalOnMissingClass


## spring.factories

springboot 会检查所有jar包下的`META-INF/spring.factories`文件，这个文件中`EnableAutoConfiguration` 的KEY下面罗列了需要自动配置的类，例如：
```
# Auto Configure
org.springframework.boot.autoconfigure.EnableAutoConfiguration=\
org.mybatis.spring.boot.autoconfigure.MybatisAutoConfiguration
```

上述的MybatisAutoConfiguration类的代码是：

``` java
@org.springframework.context.annotation.Configuration
@ConditionalOnClass({ SqlSessionFactory.class, SqlSessionFactoryBean.class })
@ConditionalOnBean(DataSource.class)
@EnableConfigurationProperties(MybatisProperties.class)
@AutoConfigureAfter(DataSourceAutoConfiguration.class)
public class MybatisAutoConfiguration {

  private final MybatisProperties properties;
  
  ...
}
```

其中MybatisProperties中可以看到我们经常在application.properties或者application.yml里的配置：
``` java
@ConfigurationProperties(prefix = MybatisProperties.MYBATIS_PREFIX)
public class MybatisProperties {

  public static final String MYBATIS_PREFIX = "mybatis";

  /**
   * Location of MyBatis xml config file.
   */
  private String configLocation;

  /**
   * Locations of MyBatis mapper files.
   */
  private String[] mapperLocations;

  /**
   * Packages to search type aliases. (Package delimiters are ",; \t\n")
   */
  private String typeAliasesPackage;

  /**
   * Packages to search for type handlers. (Package delimiters are ",; \t\n")
   */
  private String typeHandlersPackage;

  /**
   * Indicates whether perform presence check of the MyBatis xml config file.
   */
  private boolean checkConfigLocation = false;

  /**
   * Execution mode for {@link org.mybatis.spring.SqlSessionTemplate}.
   */
  private ExecutorType executorType;

  /**
   * Externalized properties for MyBatis configuration.
   */
  private Properties configurationProperties;

  /**
   * A Configuration object for customize default settings. If {@link #configLocation}
   * is specified, this property is not used.
   */
  @NestedConfigurationProperty
  private Configuration configuration;


```


## 最后做的总结就是
`auto-configuration `就是一个实现了`Configuration`接口的类。使用`@Conditional`注解来限制何时让`auto-configuration `生效，通常`auto-configuration `使用`ConditionalOnClass`和`ConditionalOnMissingBean`注解，这两注解的确保只有当我们拥有相关类的时候使得`@Configuration`注解生效。




## 创建自定义的starter

一个完整的Spring Boot starter应该包含下面这些组件：

- autoconfigure 模块包含了自动配置的代码
- starter模块提供了一个autoconfigure的模块和其他额外的依赖。

所以我决定下一篇将带领大家动手自己写一个starter，这个starter来实现一些自动配置的功能。