---
title: 通过docker启动命令的环境变量来配置spring-boot程序
tags:
  - docker
  - springboot
categories:
  - web开发
date: 2018-08-02 16:09:48
id: config-spring-boot-application-with-docker-env-var
---



配置Spring-boot程序的方式有很多种。官方文档里有详细的介绍
简单列举的话有
- 通过`application.properties`文件配置
- 通过`bootstrap.properties`文件配置
- 通过启动命令方式配置，例如 `java -jar app.jar --spring.profiles.active=test`方式
- 启动环境的变量方式配置

如果我们已经把spring-boot程序打包成了Docker镜像文件，那我们需要怎么设置程序呢？

<!-- more -->


今天小猪来分享使用docker的启动命令方式配置Spring-boot应用程序镜像的方法。
## Spring boot 程序


首先，我们的Spring-boot程序非常简单，只有一个简单的Controller文件：
``` java
@RestController
public class HomeController {

    @Autowired
    protected HttpServletRequest request;


    @RequestMapping("/ping")
    public String ping(){
        return  "pong";
    }
}
```
该Controller文件使程序启动之后能够通过hostname:port/ping的方式返回字符串`pong`,这里的设计参考了redis-cli的方法。
## 使用maven打包程序
在项目的跟目录(pom.xml目录)下执行命令：
```
mvn package
```

## 使用dockerfile文件打包成镜像
使用下属Dockerfile文件打包成镜像：
``` dockerfile
FROM openjdk:8-jdk-alpine

VOLUME /log

COPY target/smallerpig-demo-0.0.1-SNAPSHOT.jar app.jar

EXPOSE 8080

ENTRYPOINT ["java","-Djava.security.egd=file:/dev/./urandom","-jar","/app.jar"]
```
执行打包命令:
``` shell
docker build -t smallerpig-image-demo .
```
打包好之后可以使用命令：`docker images`查看镜像：

```
REPOSITORY                 TAG                 IMAGE ID            CREATED             SIZE
smallerpig-demo            latest              8732c4aae8e1        2 days ago          117MB
```



## 利用镜像启动容器

我们都知道默认的spring-boot程序使用8080端口，所以我们使用命令启动容器
``` shell
docker run --name demo1 -p 8080:8080 -d smallerpig-demo
```
启动成果之后使用命令查看镜像是否成功启动:
``` shell
$ curl localhost:8080/ping
pong
```


## 修改配置

下面我们将容器内的spring-boot程序的启动端口改成其他端口，来模拟动态配置spring-boot程序，例如这里使用6771端口。

同样的上面的镜像文件，我们使用下面命令启动容器：
``` shell
docker run --name demo2 -e SERVER\.PORT=6771 -p 6771:6771 -d smallerpig-demo
```
启动成功之后使用下面命令验证：
``` shell
$ curl localhost:6771/ping
pong
```
可以证明我们容器内部的spring-boot程序的tomcat是启动在了6771端口上面。


上面的命令核心是使用`-e SERVER\.PORT=6771`方式来设置container的环境变量，而spring-boot程序是可以读取环境变量作为启动参数的。这样我们就可以设置其他变量了，
理论上甚至使用docker的启动命令的`--env-file`参数来制定配置文件，然后完全动态配置镜像而不使用spring-boot的application.properties文件等方式的配置。

不过小猪没有尝试过这么做。我想理论归理论，也不会有人在一开始的时候就规划好完全启用spring-boot的配置文件而完全使用动态配置吧。

