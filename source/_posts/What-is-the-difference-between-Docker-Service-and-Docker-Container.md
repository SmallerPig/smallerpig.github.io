---
title: Docker的容器和服务之间的区别是啥？
tags:
  - null
categories:
  - null
date: 2018-08-01 17:07:15
id: difference-between-Docker-Service-and-Docker-Container
---

最近准备在组内施行持续集成，自然花了较多时间学习Docker的相关知识。

接下来几篇文章会重点放在Docker相关的经验方面。

我们在使用Docker过程中最经常使用的就是利用Docker来跑几个镜像，这样就不需要在机器上安装特定的服务了。

例如跑个nginx、跑个redis、mysql啥的。最大的好处是我们可以与实体机器或者虚拟机隔离的跑任意版本，且机器上不需要安装具体服务。

``` shell
docker run --name my-redis -p 6379:6379 -d redis

docker run --name my-mysql -p 3306:3306 -d mysql:5.6

```
上面的`docker run` 命令是利用特定的镜像启动一个容器，来提供服务。

还有一种方式是使用服务的方式:`docker service create ...`

那这两种方式有什么区别呢？

<!-- more -->

简而言之： `Docker service`主要用于使用`Docker swarm`配置主节点，以便docker容器可以在分布式环境中运行，并且可以轻松管理。

## Docker run： 
`docker run`命令首先在指定的镜像上创建一个可写容器，然后使用指定的命令启动它。

也就是说，`docker run`相当于API的先create 然后 start: / `containers / create then / containers /（id）/ start`

来源：https：//docs.docker.com/engine/reference/commandline/run/#parent-command

## Docker service： 
`Docker service`： 将一些较大应用程序环境中微服务的镜像。服务可能包括HTTP服务器，数据库或您希望在分布式环境中运行的任何其他类型的可执行程序。

创建服务时，您可以指定要使用的容器映像以及在运行容器中执行的命令。您还可以定义服务选项，包括：

群集将在群组外部提供服务的端口: `ports`
服务的覆盖网络，以连接到群中的其他服务: `networks`
CPU和内存限制和预留: `resources. limits. cpus. memory`
滚动更新政策
要在群中运行的镜像的副本数 :`replicas`
来源：https：//docs.docker.com/engine/swarm/how-swarm-mode-works/services/#services-tasks-and-containers

docker service 还可以使用 docker stack deploy 来方便的部署，结合docker-compose.yml文件，可以方便的管理整个程序的依赖。

例如下面的`docker-compose.yml`文件

``` yml
version: "3"
services:
  web:
    # replace username/repo:tag with your name and image details
    image: local36:5000/zhuqi-spring-boot-demo
    deploy:
      replicas: 5
      resources:
        limits:
          cpus: "0.8"
          memory: 1024M
      restart_policy:
        condition: on-failure
    ports:
      - "8080:8080"
    networks:
      - webnet
  visualizer:
    image: dockersamples/visualizer:stable
    ports:
      - "3080:8080"
    volumes:
      - "/var/run/docker.sock:/var/run/docker.sock"
    deploy:
      placement:
        constraints: [node.role == manager]
    networks:
      - webnet
networks:
  webnet:
```
当我们在目录下使用`docker stack deploy -c docker-compose.yml demo-stack`命令时，可以启动两个服务：分别是

``` shell
ID                  NAME                          MODE                REPLICAS            IMAGE                                        PORTS
lse8iuyauo8z        demo-stack_visualizer        replicated           1/1                 dockersamples/visualizer:stable              *:3080->8080/tcp
1ue59q9xtkeq        demo-stack_web               replicated           5/5                 local36:5000/zhuqi-spring-boot-demo:latest   *:8080->8080/tcp
```