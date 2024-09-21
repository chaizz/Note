---
title: Docker网络和存储
author: chaizz
date: 2021-9-29 20:51:45
tags: Docker
categories: Docker
photos: ["https://origin.chaizz.com/tc/Snipaste_2021-09-27_15-34-13.png"]

---



# 一、Docker网络和存储.



## 1. Docker三种网络 

1、 bridge 桥接网络

如果在创建容器时不指定网络，会默认将容器连接到此网络中， 使用bridge 时 宿主机可以ping通容器。容器也可以ping通宿主机，容器之间也可以相互ping通。容器之间只能通过IP访问，由于容器的IP会在重启时发生变化，因此不推荐使用IP访问。

2、Host 网络

**使用host网络可能会有安全问题**。 host 模式是与宿主机共享网络，不需要映射端口即可通过宿主机IP访问， -p 选项会被忽略。 此模式可用于优化网络性能，在容器需要处理大量端口的情况下，他不需要网络地址转换（NAT）,并且不会为每个端口创建用空间代理。

3、none

禁用容器中的所有网络，自启动容器时使用。

## 2. 自定义网络

那么我们想要我们的服务之间相互通信，如何设置网络 --> 可以使用自定义网络。

(在自定义网络中docker会使用内嵌的DNS服务器将容器名解析成IP)

查看有哪些自网络

```shell
docker network ls
```

查看某个网络的信息

```shell
docker inspect <网络名称>
```

创建自定义网络

```=N
docker network create <网络名称>
```

将某个容器连接到某个网络

```shell
docker nwtwork connect <网络名称> <容器名称/ID>
```

将某个容器与某个网络断开连接

```shell
docker nwtwork disconnect <网络名称> <容器名称/ID>
```

创建容器时指定网络

```shell
docker run -it  --rm --network <网络名称> mysql:8.0 mysql
```

**注意**：当我们在创建容器时指定了网络，那么该容器不会在连接到的默认的bridge网络。



## 3. Docker 存储

docker 提供了三种存储选项：绑定挂载、卷、临时挂载。

1、volume 卷存储在主机文件系统分配一块专有的存储区域，由DOcker进行管理，并且与主机的核心功能隔离。非Docker进行不能修改文件系统的这一部分。卷是在Docker中持久化保存数据的最佳方式。

2、bing mount 绑定挂载。他可以将主机文件系统上目录或者让文件装载到容器中，但是主机上的非DOcker进程可以修改他们，同时在容器中也可以更改主机的文件系统，包括创建、修改或者删除文件或者目录。使用不当可能会带来安全隐患。

3、tmpfs 临时挂载仅存储在主机系统的内存中，从不写入主机的文件系统。将容器停止时，数据将会被删除。

### 3.1 绑定挂载

 使用 -v 命令， 绑定挂载将主机上的目录或者文件装在到容器中，绑定挂载会覆盖容器中的目录或者文件，如果宿主机目录不存在，docker会自动创建目录但是不会创建文件。

例如：

```shell
docker run -e MYSQL_ROOT_PASSWD=123456 \
		-v /home/mysql/mysql.cnf:/etc/conf.d/mysql.cnf \
		-v /home/mysql/data:/var/lib/mysql \
		-d mysql:5.7
```

### 3.2 valume 卷

卷式Docker容器存储数据的首选方式，卷有以下的优势：

- 卷可以在多个正在运行的容器之间共享数据，仅当显示的删除卷时，才会删除卷。
- 当想要将容器数据存储在外部网路上或者云服务商的存储介质中，而不是本地时。
- 卷更加的容易备份或者迁移，当需要备份或者还原数据讲或者将一个docker主机迁移到另一个docker时卷石更好的选择。

查看当前的卷

```shell
docker volume ls
```

创建一个卷名

```shell
docker volume create <卷名称>
```

查看卷的信息

```she
docker inspect <卷名称>
```

使用卷存储, 将对应的目录修改为卷名称

```shell
docker run -e MYSQL_ROOT_PASSWD=123456 \
		-v /home/mysql/mysql.cnf:/etc/conf.d/mysql.cnf \
		-v <卷名称>:/var/lib/mysql \
		-d mysql:5.7
```

### 3.3 临时挂载

临时挂载将数据保存在宿主机的内存中当容器停止时文件被删除。

```shell
docker run -d -it --tmpfs /tmp nginx:1.22-alpine
```

