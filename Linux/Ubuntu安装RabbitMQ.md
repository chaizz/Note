---
title: Ubuntu安装RabbitMQ
author: chaizz
date: 2021-07-10 15:15:36
tags: RabbitMQ
categories: RabbitMQ
photos: ["https://tc.chaizz.com/714976f0e15011eb9d7c5254006b8f1d.jpeg"]
---

​        

<!--more-->

> **RabbitMQ**是实现了高级消息队列协议（AMQP）的开源消息代理软件（亦称面向消息的中间件）。RabbitMQ服务器是用[Erlang](https://baike.baidu.com/item/Erlang)语言编写的，而集群和故障转移是构建在[开放电信平台](https://baike.baidu.com/item/开放电信平台)框架上的。所有主要的[编程语言](https://baike.baidu.com/item/编程语言/9845131)均有与代理接口通讯的[客户端](https://baike.baidu.com/item/客户端/101081)库。

安装前提：首先需要安装Erlang

```shell
sudo apt-get install erlang-nox
```

1、 使用apt-get 安装

```
sudo apt-get update
sudo apt-get install rabbitmq-server
```

2、安装完毕配置文件默认在：/etc/rabbitmq/ 下。

**如果已经安装了redis ,再启动之前现将redis 启动。**

操作命令

```
sudo rabbitmq-server start
sudo rabbitmq-server stop
sudo rabbitmq-server restart
sudo rabbitmqctl status
```

3、创建用户

创建用户名为： admin  密码：admin

```
sudo rabbitmqctl add_user  admin  admin  
```

设置用户标签 为：administrator  （添加远程访问必须为 ：administrator  ）

```
sudo rabbitmqctl set_user_tags admin administrator
```

设置当前 / 虚拟主机下的权限：

```shell
# 第一个“.*”用于配置资产实体的权限
#第二个“.*”表示对实体的写权限
#第三个“.*” 用于读取物理的权限
sudo rabbitmqctl  set_permissions -p / admin '.*' '.*' '.*'
```

4、列出集群中的用户

```
rabbitmqctl list_users
```

5、删除用户

```
rabbitmqctl delete_user '用户名'
```

6、撤销用户在虚拟主机中的权限

```
rabbitmqctl clear_permissions -p "custom-vhost"  "用户名"
```

7、看虚拟主机列表

```
rabbitmqctl list_vhosts
```

8、每个rabbitmqctl权限管理操作的范围仅限于单个虚拟主机。

授予用户对所有虚拟主机的权限。

```shell
for v in $(rabbitmqctl list_vhosts --silent); do rabbitmqctl set_permissions -p $v "a-user" ".*" ".*" ".*"; done
```



设置RabbitMQ可以远程访问

在/etc/rabbitmq/rabbitmq-env.conf 中添加以下命令：

```shell
loopback_users=none
```

