---
title: 记一次Docker镜像瘦身[Python]
author: chaizz
date: 2022-8-4 17:28:34
tags: Docker
photos: ["https://tc.chaizz.com/tc/Snipaste_2021-09-27_15-34-13.png"]

---

​      

<!--more-->

> 前言：
>
> ​	在使用Python开发时，需要将镜像发布到私有仓库中或者阿里云仓库中，然后在K8S拉取镜像部署，但是项目经过打包后镜像大小高达3.1G。
>
> ​	每次打包镜像需要花费的时间太久且镜像拉取时间太长。



# 镜像优化历程

## 一、初始版Dockerfile内容

```dockerfile
FROM python:3.8

WORKDIR /app

COPY . .

RUN dpkg -i rocketmq-client-cpp-2.0.0.amd64.deb 

RUN pip install -i https://mirrors.aliyun.com/pypi/simple/ --no-cache-dir -r requirements.txt

RUN  ln -sf /usr/share/zoneinfo/Asia/Shanghai /etc/localtime && echo 'Asia/Shanghai' >/etc/timezone

CMD ["sh", "run.sh"]
```



构建镜像后镜像大小为3个多G

优化解决方案：

- **避免出现多层**
- **使用官方精简版本镜像**



## 二、第二版Dockerfile内容

```dockerfile
FROM python:3.8-slim

COPY . .

RUN sed -i -E 's/(deb|security).debian.org/mirrors.aliyun.com/g' /etc/apt/sources.list \
  && dpkg -i rocketmq-client-cpp-2.0.0.amd64.deb \
  && apt update -y \
  && apt install -y python3-dev default-libmysqlclient-dev build-essential \
  && pip install -i https://pypi.mirrors.ustc.edu.cn/simple/  --no-cache-dir -r requirements_base.txt \
  && apt clean -y \
  && rm rocketmq-client-cpp-2.0.0.amd64.deb \
  && rm -rf /var/cache/debconf/* \
    /var/lib/apt/lists/* \
    /var/log/* \
    /var/tmp/* \
  && rm -rf /tmp/* \
  && ln -sf /usr/share/zoneinfo/Asia/Shanghai /etc/localtime && echo 'Asia/Shanghai' >/etc/timezone
```



因为该项目使用了 `rocketmq` 所以需要使用 `rocketmq-client-cpp-2.0.0.amd64.deb` 起初打算使用 `python:3.8-alpine` 但是 `rocketmq` 没有 `alpine` 系统的包，只有 `debian` 的安装包。如果使用这个 `python:3.8-alpine`  基础镜像需要自己重新编译，所以选择了`python:3.8-slim` 这个基础镜像。

此外还需要安装mysqlclient的依赖库，所以需要更新apt源安装依赖。

将更新安装的apt依赖缓存删除。

此次镜像构建钩还有**2.6G**，依然很大。

优化解决方案：

- 避免出现多层
- 使用官方精简版本镜像
- **多层构建**



## 三、第三版DockerFile内容

```dockerfile
FROM python:3.8 as builder

COPY ./requirements_base.txt .

RUN pip install -i https://pypi.mirrors.ustc.edu.cn/simple/  --no-cache-dir -r requirements_base.txt \
&& find /usr/local/lib -name '*.pyc' -delete

FROM python:3.8-slim

COPY --from=builder   /usr/local/lib/python3.8/site-packages /usr/local/lib/python3.8/site-packages

COPY --from=builder  /usr/local/bin/celery  /usr/local/bin/celery

COPY --from=builder  /usr/lib/x86_64-linux-gnu/libmariadb.so  /usr/lib/x86_64-linux-gnu/libmariadb.so

COPY ./rocketmq-client-cpp-2.0.0.amd64.deb .

RUN  dpkg -i rocketmq-client-cpp-2.0.0.amd64.deb \
  && apt clean -y \
  && rm rocketmq-client-cpp-2.0.0.amd64.deb \
  && rm -rf /var/cache/debconf/* \
    /var/lib/apt/lists/* \
    /var/log/* \
    /var/tmp/* \
  && rm -rf /tmp/* \
  && ln -sf /usr/share/zoneinfo/Asia/Shanghai /etc/localtime && echo 'Asia/Shanghai' >/etc/timezone
```



首先将需要安装的Python依赖安装且作为一个基础镜像，然后将 `/usr/local/lib/python3.8/site-packages` 安装的依赖复制到新的镜像中。

同样需要复制 `celery`的 启动文件：`/usr/local/bin/celery ` 到新的镜像中, 否则使用 `celry -A pro worker` 会提示 `celery not found`的错误。

mysqlclient的依赖库在debian系统中是 `libmariadb.so` 也复制到 `/usr/lib/x86_64-linux-gnu/libmariadb.so` 动态库中去。



至此Python项目镜像大小由原来的**3.1G**缩小为**567M**

![](https://tc.chaizz.com/Snipaste_2022-08-04_18-05-28.png)
