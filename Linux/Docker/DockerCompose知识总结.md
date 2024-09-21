---
title: DockerCompose知识总结
author: chaizz
date: 2021-9-29 20:51:45
tags: Docker
categories: Docker
photos: ["https://origin.chaizz.com/tc/Snipaste_2021-09-27_15-34-13.png"]
---

​     

<!--more-->

# 一、DockerCompose理解

> [Docker Compose](https://docs.docker.com/compose/) 是一种旨在帮助定义和共享多容器应用程序的工具。使用 Compose，我们可以创建一个 YAML 文件来定义服务，并且使用单个命令就可以启动或关闭所有内容。
>
> 使用 Compose 的*巨大*优势是您可以在文件中定义应用程序堆栈，将其保存在项目存储库的根目录中（现在已进行版本控制），并轻松让其他人为您的项目做出贡献。有人只需克隆您的存储库并启动 Compose 应用程序即可。





Docker compose 是来管理多个容器的一个工具。

## 1、安装Docker Compose

[Docker-compose地址](https://github.com/docker/compose/releases/download/v2.0.0/docker-compose-linux-amd64)

### 1.1 创建cli-plugins 目录

```shell
# 在github上下载最新版docker-compose ,放在 docker安装用户家目录下 ：root/.docker/cli-plugins

cd .docker
mkdir cli-plugins
```

### 1.2 将下载的docker-compose-linux-amd64  改名为 docker-compose

```shell
# 在root/.docker/cli-plugins 目录下
mv docker-compose-linux-amd64 docker-compose
```

### 1.3 添加执行权限

```shell
chmod +x root/.docker/cli-plugins/docker-compose
```

### 1.4 安装低版本

```shell
curl -L https://get.daocloud.io/docker/compose/releases/download/1.29.2/docker-compose-`uname -s`-`uname -m` > /usr/local/bin/docker-compose

sudo chmod +x /usr/local/bin/docker-compose


 docker-compose --version
```



**v2.0.0版本命令为 `docker compose `中间没有横杠**



## 2、Docker Componse 官网示例（创建一个Python web应用）：

### 2.1、创建`app.py`

```python
import time

import redis
from flask import Flask

app = Flask(__name__)
cache = redis.Redis(host='redis', port=6379)

def get_hit_count():
    retries = 5
    while True:
        try:
            return cache.incr('hits')
        except redis.exceptions.ConnectionError as exc:
            if retries == 0:
                raise exc
            retries -= 1
            time.sleep(0.5)

@app.route('/')
def hello():
    count = get_hit_count()
    return 'Hello World! I have been seen {} times.\n'.format(count)
```



###  2.2、创建包文件夹 `requirements.txt`：

```txt
flask
redis
```

### 2.3 创建`Dockerfile`文件

```shell

FROM python:3.7-alpine
RUN sed -i -e 's/http:/https:/' /etc/apk/repositories
WORKDIR /code
ENV FLASK_APP=app.py
ENV FLASK_RUN_HOST=0.0.0.0
RUN apk add --no-cache gcc musl-dev linux-headers
COPY requirements.txt requirements.txt
# RUN pip install -r requirements.txt
# 指定清华源  下载快
RUN pip install -i https://pypi.tuna.tsinghua.edu.cn/simple --no-cache-dir -r requirements.txt
EXPOSE 5000
COPY . .
CMD ["flask", "run"]
```

### 2.4 创建 `docker-compose.yml`文件 ：

```yaml
version: "3.9"
services:
  web:
    build: .
    ports:
      - "5000:5000"
  redis:
    image: "redis:alpine"
```



### 2.5 在 `docker-compose.yml` 目录下运行命令

```shell
docker compose up	
```



出现错误 ：

![](https://origin.chaizz.com/tc/Snipaste_2021-09-29_19-51-36.png)



解决：在Dockerfile中添加下面这个命令

```shell
RUN sed -i -e 's/http:/https:/' /etc/apk/repositories

# 完整的Dockerfile：

FROM python:3.7-alpine
RUN sed -i -e 's/http:/https:/' /etc/apk/repositories
WORKDIR /code
ENV FLASK_APP=app.py
ENV FLASK_RUN_HOST=0.0.0.0
RUN apk add --no-cache gcc musl-dev linux-headers
COPY requirements.txt requirements.txt
# RUN pip install -r requirements.txt
# 指定清华源  下载快
RUN pip install -i https://pypi.tuna.tsinghua.edu.cn/simple --no-cache-dir -r requirements.txt
EXPOSE 5000
COPY . .
CMD ["flask", "run"]

```



### 2.5 运行成功

![](https://origin.chaizz.com/tc/Snipaste_2021-09-29_20-48-16.png)





# 二、docker compose 命令详解

结合docker compose 部署多个服务

```dockerfile
version: "3"

services:

#  web:
#    # 用来使用自定义的Dockerfile ，
#    # 使用自己构建的镜像，然后在运行容器
#    build:
#      # 启动服务是先将制定的Dockerfile 构建一个镜像。
#      context: .   #这个路径可以是相对的或者绝对的。一般是dockercompose.yml的当前路径
#      dockerfile:
#    expose: # 对外暴露端口
#      - "8000"
#
#    depends_on:  # 代表这个容器必须依赖其他的容器才能启动（服务名）
#      - my_tomcat2
#
#    ports:
#      - "8000:8000"   # 制定映射容器内端口
#
#    networks:
#      - hello



  my_tomcat2: # 服务器名称
    container_name: tomcat2   # 自定义容器名称  docker run --name
    image: tomcat   # 镜像名称
    restart: always
    ports: #  端口建议使用字符串    docker run -p
      - "8082:8080"
    volumes:
      #      - /opt/webapps:/usr/local/tomcat/webapps # 自定义路径映射
      - "./env/webapps:/usr/local/tomcat/webapps" #使用数据库名映射
    networks:
      - hello


    depends_on: # 代表这个容器必须依赖其他的容器才能启动（服务名）
      - my_mysql
      - my_redis

    healthcheck: # 通过命令检测容器是否正常运行
      test: [ "CMD","curl","-f","http://localhost" ]
      interval: 1m30s
      timeout: 10s
      retries: 3


#    ulimits:   # 用来修改容器内部的最大进程数
#      nproc: 65535
#      nofile:
#        soft: 20000
#        hard: 40000


#    sysctls:   # 用来修改容器内部内核参数，并不是必须的，有些容器启动收到操作系统参数限制需要用到这个命令
#      - net.core.somaxconn=1024
#      - net.ipv4.tcp_syncookies=0





  my_mysql:
    image: mysql:5.7
    container_name: mysql
    privileged: true
    restart: always
    ports:
      - "3306:3306"
    volumes:
      - "./env/mysql/conf.d:/etc/mysql/conf.d"
      - "./env/mysql/mysqldata:/var/lib/mysql"
      - "./env/mysql/mysqllogs:/logs"
#    environment:
#      MYSQL_ROOT_PASSWORD: "123456"
#      TZ: Asia/Shanghai

    env_file:  # 将环境变量中的值设置在文件中  必须是以.env 结尾的文件   必须要是键值对的形式或者A=B的形式
      - ./env/mysql/mysql.env    # 这个文件中就写   MYSQL_ROOT_PASSWORD: "123456"就可以

    # environment和env_file 写一个就可以

    command:
      --wait_timeout=31536000
      --interactive_timeout=31536000
      --max_connections=1000
      --default-authentication-plugin=mysql_native_password
    networks:
      - hello


  my_redis:
    image: redis
    container_name: redis
    restart: always
    ports:
      - "6379:6379"
    expose:
      - "6379"
    volumes:
      - ./env/redis/data:/data
      - ./env/redis/conf:/usr/local/etc/redis
      - ./env/redis/logs:/logs

    # 注意 redis.conf 中的 daemonize no   要设置为前台运行。
    #要不然运行了docker compose up , redis自动关闭
    command: redis-server /usr/local/etc/redis/redis.conf
    networks:
      - hello


  nginx:
    container_name: nginx
    restart: always
    image: nginx:1.11.6-alpine
    ports:
      - "8080:80"
      - "80:80"
      - "443:443"
    volumes:
      - ./env/nginx/conf.d:/etc/nginx/conf.d
      - ./env/nginx/logs:/var/log/nginx
      - ./env/nginx/www:/var/www
      - /etc/letsencrypt:/etc/letsencrypt
    networks:
      - hello


  # compose 中运行 portainer
  portainer:
    image: portainer/portainer
    container_name: portainer
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock
      - /opt/portainer/data:/data
    ports:
      - 8000:8000
      - 9000:9000
    networks:
      - hello


#volumes:
#  tomcat1webapps:   # 声明指定的卷名，compose会自动加上docker-compose.yml 的外层目录名
#    external:   # 使用自定义的卷名，前提必须存在：需要手动执行docker volume create tomcatwebapps
#      false
#  webapps:


networks:   # 声明上面创建的 hello 网桥
  hello:
    external:   # 使用自定义的网桥，前提必须存在 ：需要手动执行docker networks create -d bridge hello
      false




# docker compose up                ：启动所有服务
    #   -f  ：指定dockercompose.yml 文件
    #   -p  ：指项目名称
    #   -d  ：设置所有服务后台运行
#   docker compose down         ：关闭dcokercompose所有服务，会移除网络，不会移除数据卷
#   docker compose exec  [服务名]  ：进入服务对应的容器
#   docker compose ps           ：列出所有的容器
#   docker compose restart   [服务名]  ：列出所有的容器
#   docker compose rm   [服务名]  ：列出所有的容器
    # -f 强制删除
    # -v 删除服务对应容器挂载的数据卷
#   docker compose start   [服务名]  ：启动所有的容器
#   docker compose stop   [服务名]  ：停止所有的容器 （不会移除网络）
#   docker compose top   [服务名]  ：查看所有容器内运行的进程
#   docker compose unpause   [服务名]  ：将挂起的服务恢复
#   docker compose pause   [服务名]  ：挂起某一个服务
#   docker compose logs   [服务名]  ：查看所有服务的日志

```

# 三、安装Portainer是Docker可视化软件

```dockerfile
# 下载
docker pull portainer/portainer
# 启动容器
docker run -p 9000:9000 --name=portainer --restart=always -v /opt/portainer/data:/data -v /var/run/docker.sock:/var/run/docker.sock portainer/portainer

#下载汉化版  指定汉化版镜像

docker run --name=portainer -p 9000:9000 --restart=always -d  --rm  -v /var/run/docker.sock:/var/run/docker.sock -v /opt/portainer/data:/data registry.cn-shenzhen.aliyuncs.com/infrastlabs/portainer-cn

# 访问公网IP xxx:9000   设置默认密码 ：至少八位
```

