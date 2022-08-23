---
title: DockerSwarm理解
author: chaizz
date: 2021-9-29 20:51:45
tags: Docker
categories: Docker
photos: ["https://tc.chaizz.com/tc/Snipaste_2021-09-27_15-34-13.png"]
---



# 一、DockerSwarm理解

> 官网解释：
>
> Docker Engine 1.12 introduces swarm mode that enables you to create a cluster of one or more Docker Engines called a swarm. A swarm consists of one or more nodes: physical or virtual machines running Docker Engine 1.12 or later in swarm mode.
>
> Docker Engine 1.12 引入了 swarm 模式，使您能够创建一个由一个或多个 Docker 引擎组成的集群，称为 swarm。 swarm 由一个或多个节点组成：在 swarm 模式下运行 Docker Engine 1.12 或更高版本的物理或虚拟机。



<!--more-->

[官网地址](https://docs.docker.com/engine/swarm/)

![](https://tc.chaizz.com/tc/swarm-diagram.png)

## 1、Swarm 中有两种类型的节点：

### 1.1、managers 的作用

- 维护集群状态
- 集群调度
- 给集群提供API

Swarm 使用Raft来保证分布式一致性。

> [Raft ](https://raft.github.io/)是一种更为简单方便易于理解的[分布式算法](https://baike.baidu.com/item/分布式算法/1372646)，主要解决了分布式中的一致性问题。相比传统的[Paxos算法](https://baike.baidu.com/item/Paxos算法/6632960)，Raft将大量的计算问题分解成为了一些简单的相对[独立](https://baike.baidu.com/item/独立/3259)的子问题。
>
> 相比于传统的一致性算法Paxos，Raft有一些自己的独特的特性。比如增加了强领导性，优化了领导的选举过程，在成员发生变化之后依然能够很好的进行工作。



三个管理器的群体最多可以容忍一个管理器的损失。
一个五管理器群可以容忍最大同时丢失两个管理器节点。
N 个管理器集群最多可以容忍 (N-1)/2 个管理器的丢失。
Docker 建议一个集群最多有七个管理器节点。

在一些分布式任务重，节点个数往往是奇数。是为了能够成功选举出领导，如果是偶数肯能会有得票一致的情况。

### 1.2、workers

worker 节点 就是docekr引擎的实例，他的唯一的目的就是执行容器。worker节点不参与 Raft 分布式状态，不做出调度决策，也不为群模式 HTTP API 提供服务。



# 二、创建集群

## 1、初始化集群

```shell
# 初始化主节点，使用主节点创建不同类型的节点令牌
docker swarm init --advertise-addr 内网地址
```



```shell
# 创建manager令牌
docker swarm join-token manager

# 创建worker令牌
docker swarm join-token worker

# 加入一个节点
docker swarm join ---token 令牌
```



```shell
# 查看节点名称
docker node ls
```



# 三、Docker 服务弹性扩容

```shell
# 在Swarm manager 节点下

# 启动一个服务，具备扩缩容的功能
docker service create -p 8888:80 nginx

# 直接启动三个副本。
docker service update --replicas 3 nginx

```

