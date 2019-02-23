---
title: docker swarm vs kubernetes
tags:
  - null
categories:
  - null
date: 2018-08-10 16:24:48
id: docker-swarm-or-kubernetes
---


我们在把程序打包成镜像之后，需要将镜像跑起来，形成个容器(container)。

多个相同镜像的容器一起跑可以组成服务(service)。

多个服务跑起来可以组成一个应用(app)

那么怎么管理应用呢？

官方提供了`docker swarm`,谷歌提供了`kubernetes`

那这两者之间孰优孰劣呢？

<!-- more -->

docker官方介绍kubernates 的一张图
![](https://ws4.sinaimg.cn/large/006tNbRwgy1fu4pjxyfz3j314z0te78f.jpg)




You should run all of your work on nodes and not on masters. This may differ from Docker Cloud where you may have run some work on manager nodes.



this command deploys a Docker application, named test-app, from a YAML configuration file called app1.yml:
```
$ docker stack deploy -c app1.yml test-app
```
This command deploys a Kubernetes application from a YAML manifest file called k8s-app1.yml:
```
$ kubectl create -f k8s-app1.yml
```