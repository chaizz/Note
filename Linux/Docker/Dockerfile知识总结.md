---
title: Dockerfile
author: chaizz
date: 2021-11-20 17:24:11
tags: Docker、Linux
photos: ["https://tc.chaizz.com/tc/Snipaste_2021-09-27_15-34-13.png"]
---

​            

<!--more-->



> 使用Dockerfile 能够构建一个自定义镜像。通常情况下是将自己的应用打包成镜像，在容器中使用。



## Dockerfile 命令

| 命令       | 作用                                                         |
| ---------- | ------------------------------------------------------------ |
| FROM       | 表示当前镜像是基于那个镜像                                   |
| MAINTAINER | 镜像的维护者和邮箱                                           |
| RUN        | 构建镜像的时候需要运行的命令                                 |
| EXPOSE     | 容器内对外暴露的端口                                         |
| WORKDIR    | 指定创建容器后默认进入容器的工作目录                         |
| ENV        | 用来构建镜像过程中设置的环境变量 key=value                   |
| ADD        | 将宿主机下的文件拷贝到容器中，且会自动处理url和压缩包        |
| COPY       | 类似于ADD，将从构建的Dockerfile目录中复制镜像的目录中        |
| VOLUME     | 容器数据卷                                                   |
| CMD        | 制定一个容器运行的命令，如果有多个命令，只执行最后一个。会被docker run 后面的命令替代 |
| ENTRYPOINT | 指定一个容器启动时要运行的命令和参数，和CMD一样              |

###  1、FROM 命令语法

```dockerfile
FROM <image>
FROM <image>[:<tag>]   # 设置镜像的版本，不写为最新版本  latest

```

### 2、RUN 命令

```dockerfile
# RUN 后面直接写shell命令, RUN 的命令是在构建镜像中执行。
RUN echo hello word

RUN ["yum","install","-y","vim"]
```

### 3、EXPOSR 命令

```dockerfile
# 针对一些需要端口的服务
EXPOSE 6379
```

### 4、WORKDIR 命令

```dockerfile
# 用来为Dockerfile中的任何 RUN/CMD/ENTRYPOINT/CPOY/ADD/ 指令设置工作目录，如果WORKDIR  不存在，即使他没有在后续得命令中被使用，也将会被创建。

WORKDIR /opt/myserver
WORKDIR /aaa
WORKDIR bb

# WORKDIR 可以使用多次，如果使用的是相对命令 ，会与上一次的路径相对

# 如上面的命令:docker会创建/opt/myserve，也会创建/aaa/bb，在进入容器内会进入最后的WORKDIR的路径下即：/aaa/bb
```

### 5、ADD 命令

```dockerfile
# 用来从Dockerfile的当前目录中复制文件，目录，或者下载URL,并将他们添加到位于容器内指定的文件中

ADD bb.txt /aaa/bb
ADD https://tc.chaizz.com/497261de494511ec9d7c5254006b8f1d.png /aaa/bb
# 可以将文件复制到容器中，也可以通过URL下载文件到容器中


# 将 apache-tomcat-8.5.73-src.tar.gz 解压到 /aaa/bb 
ADD apache-tomcat-8.5.73-src.tar.gz /aaa/bb
# 将apache-tomcat-8.5.73-src 改名 为tomcat
RUN mv apache-tomcat-8.5.73-src  tomcat
# 将工作目录改为 tomcat
WORKDIR tomcat

```

### 6、COPY 命令

```dockerfile
# 将文件复制到指定容器内部的目录
# COPY a.txt /aaa/bb
```



### 7、volume 目录

```dockerfile
# 允许在容器运行的时候将目录挂载到宿主机的目录上
VOLUME /aaa/bb/tomcat/webapps

```



### 8、ENV 命令

```dockerfile
# 设置当前容器的环境变量

ENV BASE_DIR

```



### 9、ENTRYPOINT 命令

```dockerfile
# 容器运行的命令和参数  ENTRYPOINT的命令是在容器构建完成之后启动的时候执行,要覆盖ENTRYPOINT  他的指令 需要在docker run 的时候加上 --entrypoint ls 
ENTRYPOINT ls $BASE_DIR/tomcat
# 或者
ENTRYPOINT ["ls", "$BASE_DIR/tomcat"]
```



### 10、CMD 命令

```dockerfile
# 只执行最后一个，如果在 docker run 之后指定了命令(不需要参数) 会将CMD的指令覆盖，不再执行CMD的指令

CMD ls $BASR_DIR
# 或者
CMD ["ls", "$BASR_DIR"]

# 通常 CMD 和 ENTRYPOINT 结合使用 ：ENTRYPOINT作为固定的命令或者参数， CMD 作为可变的参数，附加在ENTRYPOINT命令之后。 然后ENTRYPOINT + CMD 结合为一条命令。  即可实现在docker run 的时候可以指定参数运行同一个容器
```

