---
title: Docker Compose和Docker Stack之间的区别
tags:
  - docker
categories:
  - web开发
date: 2018-07-31 15:08:59
id: difference-docker-compose-and-docker-stack
---

在最近的版本中，Swarm模式已经在1.12中集成到Docker Engine中，并带来了几个新工具。其中，可以使用docker-compose.yml文件来启动docker 服务，而无需安装Docker Compose。

该命令称为`docker stack`，它看起来与`docker-compose`完全相同。

那他们之间在使用上有什么区别呢？

<!-- more -->
例如下面的代码：

```
$ docker-compose -f docker-compose up

$ docker stack deploy -c docker-compose.yml somestackname

```

确实很相似。这两个都将提供所有服务，卷，网络和其他所有内容，就像`docker-compose.yml`文件中指定 的那样。但为什么会发生这种情况，并且docker stack与Docker Compose有什么不同呢？为什么要介绍？除了语法之外，还有什么
需要我们在使用的时候注意的呢？

## 区别
`Docker stack`忽略了`build`指令。您无法使用stack命令构建新镜像。它需要预先存在的镜像才能正确运行。所以`docker-compose`更适合开发场景。

还有一些compose-file 规范的部分被 docker-compose或stack命令忽略。

Docker Compose是一个Python项目。最初，有一个名为fig的Python项目，用于解析fig.yml文件。

在内部，它使用Docker API根据规范调出容器。您仍然需要单独安装docker-compose以在计算机上与Docker一起使用。

Docker引擎功能包含在Docker引擎中。您无需安装其他软件包即可使用它部署docker stacks是swarm模式的一部分。它支持相同类型的撰写文件，但处理发生在Dock代码内部的Go代码中。您还必须在能够使用`docker stack`命令之前创建一个单机“swarm”，但这不是一个大问题。

Docker堆栈不支持根据版本2规范编写的docker-compose.yml文件。它必须是最新的，最新版本为3，而Docker Compose仍然可以毫无问题地处理版本2和3。

## 结论
docker-compose和新的docker stack命令都可以与docker-compose.yml文件一起使用，这些文件是根据版本3的规范编写的。对于你的版本2依赖项目，你将不得不继续使用docker-compose。

由于docker stack完成了docker compose所做的一切，所以可以肯定的是 docker stack将占上风。这意味着docker-compose可能会被弃用，最终将不再受支持。

但是，对于大多数用户而言，将工作流切换为使用docker堆栈既不困难也不开销。您可以在将docker compose文件从版本2升级到3时以相对较低的工作量完成此操作。

如果您是Docker世界的新手，或正在选择用于新项目的技术 - 无论如何，请坚持使用docker stack deploy。
