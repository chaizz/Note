---
title: Docker知识总结
author: chaizz
date: 2021-9-27 15:33:15
tags: Docker、Linux
photos: ["https://tc.chaizz.com/tc/Snipaste_2021-09-27_15-34-13.png"]
---

> Docker is an open platform for developing, shipping, and running applications. Docker enables you to separate your applications from your infrastructure so you can deliver software quickly. With Docker, you can manage your infrastructure in the same ways you manage your applications. By taking advantage of Docker’s methodologies for shipping, testing, and deploying code quickly, you can significantly reduce the delay between writing code and running it in production.

<!--more-->

# 一、Docker 是什么

## 1、Docker 简介

 Docker 是一个用于开发、传送和运行应用程序的开放平台。Docker 使您能够将应用程序与基础设施分开，以便您可以快速交付软件。使用 Docker，您可以像管理应用程序一样管理基础设施。通过利用 Docker 的快速交付、测试和部署代码的方法，您可以显着减少编写代码和在生产中运行代码之间的延迟。

Docker是基于Google的Go语言开发的。

## 2、Dcoker 能干什么？

Docker 能方便的将开发环境打包起来，包括Python、Nginx 、Redis、Mysql、Tomcat等等的开发环境只需要一次部署，然后将他们打包为镜像，接下来只需要拿着这个镜像在不同的服务器中的docker中运行即可得到一个全新的与之前部署的环境完全一样的环境。有点类似于布尔玛的万能胶囊，只是有点..

# 二、Docker与虚拟机的区别

> https://zhuanlan.zhihu.com/p/271846374



![](https://tc.chaizz.com/tc/Snipaste_2021-09-27_16-03-32.png)

传统的虚拟机技术：由于虚拟机是把这个系统内核、系统依赖、硬件资源配置全部打包，组成一个新的系统。他是在硬件的基础上实现虚拟化。所以虚拟机占用的资源比较大，切换比较耗时。

Docker技术：它是直接运行在宿主机上，是系统层面的虚拟化，他不需要自己拥有单独的内核，也没有打包硬件资源，所以他是很轻便的，而且每个容器之间是相互隔离的，类似于一个沙箱。



| 特性       | 容器               | 虚拟机                   |
| ---------- | ------------------ | ------------------------ |
| 启动       | 秒级·              | 分钟级                   |
| 硬盘空间   | 一般为几十MB       | 一般为GB                 |
| 性能       | 接近原生           | 弱于原生                 |
| 系统支持量 | 单机支持上千个容器 | 一般几十个               |
| 操作系统   | 与宿主机共享OS     | 在宿主机上运行虚拟机的OS |



Docker的优点：

- 对系统资源能够更高效的利用。
- 启动速度更快。
- 一致的运行环境。
- 维护和扩展更加的容易。
- 应用更快的交付与部署



# 三、Docker 的基本组成

![](https://tc.chaizz.com/tc/Snipaste_2021-09-27_16-23-06.png)

## Docker的三大组件：

### 1、镜像：

Docker的镜像好比一个文件模板（分层的），有各种各样的模板，这个模板就是一些具体的应用以及对应的资源。比如说一个Nginx 镜像 就是一个包含Nginx的模板。他需要在一个Ubuntu或者其他系统镜像上安装那么就是一个可运行的Nginx镜像。

### 2、容器：

容器类似于一个沙箱，是基于镜像来创建的，多个镜像可以组成一个容器，他是独立运行的，可以启动、暂停、删除容器等操作。

### 3、仓库：

仓库就是用来存放镜像的地方，这个概念和git是一样的，有共有仓库和私有仓库。



# 四、Dcoker的安装与卸载

##  1、Ubuntu 18.04 手动安装下安装

> 安装前提：
>
> 系统：Ubuntu 18.04 LTS   
>
> 系统内核 ：4.15.0-136-generic

具体的操作系统根据Docker的[官网](https://docs.docker.com/engine/install/ubuntu/)来选择不同的安装方法。

### 1、卸载旧版本

```shell
sudo apt-get remove docker docker-engine docker.io containerd runc
```

### 2、更新 apt 包索引并安装包以允许 apt 通过 HTTPS 使用存储库

```shell
sudo apt-get update
 
sudo apt-get install \
    apt-transport-https \
    ca-certificates \
    curl \
    gnupg \
    lsb-release
```

### 3、添加Docker官方的GPG密钥

```shell
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg

```

### 4、设置稳定存储库

```shell
echo "deb [arch=amd64 signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
```

### 5、安装Docker

```shell
sudo apt-get update
sudo apt-get install docker-ce docker-ce-cli containerd.io
```

### 6、验证是否安装成功

```shell
sudo docker run hello-world
```

### 7、卸载Docker

```shell
sudo apt-get purge docker-ce docker-ce-cli containerd.io

sudo rm -rf /var/lib/docker

sudo rm -rf /var/lib/containerd
```

### 8、配置阿里云镜像加速

直接在阿里云首页搜索**容器镜像服务**，登录注册后 --> 进入镜像工具 --> 镜像加速器。具体页面如下：

![](https://tc.chaizz.com/tc/Snipaste_2021-09-27_17-03-26.png)

```shell
sudo mkdir -p /etc/docker

sudo tee /etc/docker/daemon.json <<-'EOF'
{
  "registry-mirrors": ["https://1xxxxxx5.mirror.aliyuncs.com"]
}
EOF

sudo systemctl daemon-reload

sudo systemctl restart docker

```

 ## 2、官网也提供了一种方便的脚本安装方法：

### 1、只需要两条命令：

```shell
curl -fsSL https://test.docker.com -o test-docker.sh

sudo sh test-docker.sh
```

运行第一条命令后可能会出现以下错误：

```shell
curl: (35) OpenSSL SSL_connect: SSL_ERROR_SYSCALL in connection to get.docker.com:443
```

原因大概就是无法访问外国的网站，加个提子就行。

接下来运行 执行脚本命令。即可等待自动安装。

使用以下命令可查看 在执行命令过程中运行了那些命令：

```shell
DRY_RUN=1 sh ./get-docker.sh
```

### 2、安装完毕后检查docker是否启动

```shell
systemctl status docker
```

### 3、启动docker

```shell
systemctl start docker
```

### 4、安装完毕后查看是否正确安装：(提示docker的一些信息)

```shell
docker info 
```

### 5、设置docker开机自动启动

```shell
systemctl enable docker

Created symlink /etc/systemd/system/multi-user.target.wants/docker.service → /usr/lib/systemd/system/docker.service.
```

### 6、建立docker组 将root用户加入docker组 （此步骤可做可不做）

```shell
groupadd docker

usermod -aG docker $USER
```

### 7、重启docker

```shell
systemctl restart docker
```

### 8、配置镜像加速与上面一致



### 9、安装docker-compose v2

```shell
# 创建目录  针对所有用户
mkdir -p /usr/local/lib/docker/cli-plugins
# 或者在家目录下创建  针对当前用户
# mkdir -p /.docker/cli-plugins


# 下载二进制版本文件 地址：https://github.com/docker/compose/releases
wget https://github.com/docker/compose/releases/download/v2.1.1/docker-compose-linux-x86_64 -O docker-compose

# 设置为可执行文件
chmod +x /usr/local/lib/docker/cli-plugins/docker-compose

# 检查是否安装成功
docker compose version
```







# 五、Docker 基本命令

[Docker 官网文档命令](https://docs.docker.com/reference/)

## 1、帮助命令：

```shell
# 显示docker的系统信息，包括镜像和容器的数量
docker info

#显示docker版本
docker version

# 帮助信息
docker --help
```

## 2、镜像基础命令

```shell
# 查看镜像
docker images

-a   显示全部的镜像
-q   只显示全部的镜像ID



# 搜索镜像
docker search <镜像名>

--filter=starts==300   根据镜像starts搜索，大于3000的



# 下载镜像，如果不写版本默认拉取最新版
docker pull <镜像名>[:tag]



# 指定镜像版本
docker pull mysql:5.7



# 删除指定的镜像  （可删除多个镜像）
docker rmi <镜像ID>   <镜像ID>   <镜像ID> 
-f  强制删除

# 强制删除全部的镜像
docker rmi -f $(docker images -aq)



# 通过Dockerfile 构建镜像   myimage：镜像名  0.0.1：镜像名标签
docker build -t myimage:0.0.1

```

## 3、容器基础命令

``` shell
# 新建容器并启动
docker run [--options] images

--name="Name"   给容器起别名

#通过后台的方式启动的时候，在使用docker ps 发现没有正在运行的容器，是因为docker发现前台没有使用当前容器就会自动杀死当前容器。 
-d             后台方式启动 。



-it            使用交互方式运行，进入容器查看内容
-p             指定容器的端口    8080:8080
	-p IP:主机端口：容器端口 (常用)
	-p 主机端口：容器端口 (常用)
	-p 容器端口
	
-P             随机指定端口



# 列出当前所有正在运行的容器
dcoker ps

-a             列出当前所有正在运行的容器和历史运行的容器
-n=?           列出最近运行的容器
-q             只显示容器的ID

-v 
--rm            容器退出升级删除挂载的数据卷  （在调试时使用）

#直接停止容器并退出容器
exit

#不停止容器并退出容器
ctrl + p + q


# 删除指定容器
dcoker rm 容器ID


# 强制删除所有容器
dcoker rm -f  %(dcoker ps -aq)


# 启动容器
docker start 容器ID


# 重启容器
docker restart 容器ID


# 停止容器
docker stop 容器ID


# 强制停止容器
docker kill 容器ID
```

## 4、其他常用命令

```shell
# 查看docker日志
docker logs  -tf --tail 10 容器ID

-t            显示时间戳
-f            跟踪日志输出
--tail 10     输出指定行数       


# 查看容器进程信息
docker top 容器ID

# 查看容器的元数据
docker inspect 容器ID


# 进入当前正在运行的容器  (以交互式，进入容器开启一个新的终端)
docker exec -it 容器ID /bin/bash

# 进入当前正在运行的容器  (进入正在运行的命令终端，如果没有进入默认终端)
docker attach 容器ID


#从容器内拷贝文件到宿主机上
docker cp 容器Id:文件路径  目的主机路径 

# 查看数据卷的信息 xxx:数据卷名
docker inspect xxx

# 删除挂载的数据卷   xxx:数据卷名
docker volume rm xxx

```



# 六、运行常见服务容器



## MySQL

```shell
## 创建MySQL配制间文件以及数据文件挂载目录
mkdir /opt/mysqldata
mkdir /opt/mysqlconf

# 拉取指定版本镜像
docker pull mysql:5.7

# 运行容器
docker run --name mysql-name -p 3307:3306 --restart=always -v /opt/mysql_volume/mysqldata:/var/lib/mysql -v /opt/mysql_volume/mysqllogs:/var/log/mysql -e MYSQL_ROOT_PASSWORD=123456 -d mysql:5.7 --character-set-server=utf8mb4
```



## Redis

```shell
## 创建Redis配制间文件以及数据文件挂载目录
mkdir /opt/redisdata    存放redis rdb 持久化的数据
mkdir /opt/redisconf   # 存放redis 的配置文件 redis.conf  

# 拉取指定版本镜像
docker pull redis:5.0.1

# redis 配置文件 

bind 0.0.0.0
port 6388
appendonly yes
requirepass 123456



# 指定配置文件启动
docker run -d --name redis-name -p 6388:6388 --restart=always -v /opt/redis_volume/redisdata:/data -v /opt/redis_volume/redisconf:/usr/local/etc/redis -e TZ=Asia/Shanghai redis:5.0.1 redis-server /usr/local/etc/redis/redis.conf
```



## Elasticsearch 、Kibana



```shell
# 创建网桥 ， 可以不用。
docker network create somenetwork

#  -e "discovery.type=single-node"   以单节点的形式启动 ， 可以不加
docker run -d --name elasticsearch --net somenetwork -p 9200:9200 -p 9300:9300 -e "discovery.type=single-node" elasticsearch:tag
```



使用docker ps 查看，发现elasticsearch没有正在运行， 使用docker logs -f  容器ID 提示一下错误：大致就是没有足够的内存供 Java 运行时环境继续使用。

![](https://tc.chaizz.com/497261de494511ec9d7c5254006b8f1d.png)

解决办法：

```shell
# 打开文件
vi /etc/sysctl.conf

# 在末尾加上  值为262144或者更大。
vm.max_map_count = 262144

# 保存 启用
sysctl -p 
```





```shell
# 挂在数据和配置文件  （单节点启动）

docker pull elasticsearch:7.14.2

docker run -d --restart=always --name elasticsearch -p 9200:9200 -p 9300:9300  -v esdata:/usr/share/elasticsearch/data -v esconfig:/usr/share/elasticsearch/config -v esplugins:/usr/share/elasticsearch/plugins -e "discovery.type=single-node" elasticsearch:7.14.2

```



下载 Kibana 

```shell
docker pull kibana:7.14.2
```

```shell
docker run -d --restart=always --name kibana -p 5601:5601 -e "ELASTICSEARCH_HOSTS=http://101.35.188.40:9200" -e "I18N_LOCALE=zh-CN" kibana:7.14.2


# 将kibana的配置文件挂载出来，在配置文件中连接elasticsearch
docker run -d --restart=always --name kibana -p 5601:5601 -v kibanaconf:/usr/share/kibana/config -e "I18N_LOCALE=zh-CN" kibana:7.14.2

```

