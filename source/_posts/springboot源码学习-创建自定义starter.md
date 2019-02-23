---
title: springboot源码学习-创建自定义starter
tags:
  - springboot
categories:
  - java
date: 2018-12-25 23:19:06
id: 20181225
---


上一篇的对auto-configuration的学习其实在正在开发过程中可能并不会用到，但是如果我们想开发一些公共的组件或者类库的话就希望能够给到一些默认的配置，在使用者直接引入相关依赖的时候不仅能够开箱即用，还能根据自己的需求更改配置，这个时候最好的方式就是给使用者提供一个`starter`了

<!-- more -->



创建自定义的starter
一个完整的Spring Boot starter应该包含下面这些组件：

`autoconfigure` 模块包含了自动配置的代码
`Properteis`属性类
`starter`模块提供了一个`autoconfigure`的模块和其他额外的依赖。

核心的无非也就这两个类了，我们来新建一个maven项目之后创建这两个类：
``` java
@ConfigurationProperties(prefix = "person")
public class DemoProperties {

    private String message = "hello world";

    private Integer age = 32;

    private String name = "smallerpig"; 
    //这里都给信息填写上默认值，当application.properties文件里没有这些值的时候则使用这些值来作为默认

    public String getMessage() {
        return message;
    }

    public void setMessage(String message) {
        this.message = message;
    }

    public Integer getAge() {
        return age;
    }

    public void setAge(Integer age) {
        this.age = age;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }
}



@Configuration//开启配置
@EnableConfigurationProperties(DemoProperties.class)//开启使用映射实体对象
@ConditionalOnBean(DemoService.class)
@ConditionalOnProperty//存在对应配置信息时初始化该配置类
        (
                prefix = "person",//存在配置前缀person
                value = "enabled",//开启
                matchIfMissing = true//缺失检查
        )
public class DemoConfiguration {

    @Autowired
    private DemoProperties demoProperties;

    @Bean
    @ConditionalOnMissingBean(DemoService.class)
    public DemoService demoService(){
        System.out.println("begin create DemoService");

        DemoService demoService = new DemoService();
        demoService.setAge(demoProperties.getAge());
        demoService.setMessage(demoProperties.getMessage());
        demoService.setName(demoProperties.getName());

        return demoService;
    }


}
```
为了配合演示，我新建了另外一个类：`DemoService`，用来获取自动配置里面的值

``` java
public class DemoService {
    private String message;
    private Integer age;
    private String name;
    public String sayHello(){
        String result = this.name +" is " + this.age + " and said the message :" + this.message;
        System.out.println(result);
        return result;
    }

    public void setMessage(String message) {
        this.message = message;
    }

    public void setAge(Integer age) {
        this.age = age;
    }

    public void setName(String name) {
        this.name = name;
    }
}
```


从上一篇中我们知道，springboot 会检查所有jar包下的META-INF/spring.factories文件，这个文件中EnableAutoConfiguration 的KEY下面罗列了需要自动配置的类，例如：

所以我们在项目的resources.META-INFO目录下新建一个spring.factories文件，文件内容为：
``` properties
org.springframework.boot.autoconfigure.EnableAutoConfiguration=com.zhuqi.study.DemoConfiguration //对应的配置类所在完整包名
```

这时，我们完整的一个starter项目就已经完成了。

怎么使用呢？我们来尝试着使用：

新建另外一个项目，加入对starter的引用：
``` xml
<dependencies>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
        <version>1.5.9.RELEASE</version>
    </dependency>
    <dependency>
        <groupId>com.zhuqi</groupId>
        <artifactId>study-starter</artifactId>
        <version>1.0-SNAPSHOT</version>
    </dependency>
</dependencies>
```

新建一个简单的测试启动类：
``` java
@SpringBootApplication
@RestController
public class Boot {

    public static void main(String[] args){

        SpringApplication.run(Boot.class);
    }

    @Autowired
    private DemoService demoService;

    @RequestMapping("/hello")
    public String hello(){
        return demoService.sayHello();
    }
}
```

不添加任何配置，启动看看我们程序的自动配置生效了没？

![](https://ws1.sinaimg.cn/large/006tNc79gy1fzmijgouq2j30ve0k0wjl.jpg)

新建application.properties里，添加person.name=zhuqi

重新启动项目看看效果：
![](https://ws2.sinaimg.cn/large/006tNc79gy1fzmil87a1cj30se0guwhz.jpg)


说明我们的写的程序成功了。

最后简单说下最近的动态，越发的发现到了小猪的这个年纪，单纯的靠写代码营生已经越来越力不从心了，每天的精力感觉总是不够用，一到了特定的时间节点根本不能够集中注意力来做事；而所谓的转管理、写框架对于我来说也并非易事，所以只能想着靠自己的影响力来提升团队的效率。

可不是么，怎么去跟现在刚毕业的小伙子们拼体力。

另外一方面，公司本身的业务需要，白天很少有整块的时间来让自己集中精力研究技术，而晚上如果回到家了，老婆小孩都一天没见到你了，作为家庭的一份子，不会让你安静的坐在角落里学习，这就产生了某种矛盾，一方面是工作上的进步，另一方面是家庭的需要，这两种矛盾怎么平衡也是考验我们这个大龄码农的难题。


欢迎您关注我的个人公众号`大龄码农乐园`，来听听更多的关于我们这个年纪的码农的心声。

![](https://ws4.sinaimg.cn/large/006tNc79gy1fzmj06wkutj3076076q3e.jpg)