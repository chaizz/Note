---
title: Docker部署常见应用[持续更新中]
date: 2022-2-19 21:00:00
author: chaizz
tags: Docker部署应用
categories: Docker
photos: ["https://origin.chaizz.com/1d3f92b2918411ec84fb0242ac140002.png"]
cover: "https://origin.chaizz.com/1d3f92b2918411ec84fb0242ac140002.png"
---

​               

<!--more-->

## 一、Nginx

```yaml
version: '3'
services:
  nginx:
    image: nginx
    container_name: nginx
    ports:
      - 80:80
      - 443:443
    volumes:
      - /opt/apps/web_apps:/opt/apps/web_apps
      - /opt/apps/nginx/cert:/opt/apps/nginx/cert
      
      - /opt/apps/nginx/nginx.conf:/etc/nginx/nginx.conf
      - /opt/apps/nginx/conf.d:/etc/nginx/conf.d
      - /opt/apps/nginx/logs:/var/log/nginx
    restart: always

```

### 1. nginx.conf 默认配置

```nginx
user  nginx;
worker_processes  1;

error_log  /var/log/nginx/error.log warn;
pid        /var/run/nginx.pid;


events {
    worker_connections  1024;
}


http {
    include       /etc/nginx/mime.types;
    default_type  application/octet-stream;

    log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                      '$status $body_bytes_sent "$http_referer" '
                      '"$http_user_agent" "$http_x_forwarded_for"';

    access_log  /var/log/nginx/access.log  main;

    sendfile        on;
    #tcp_nopush     on;

    keepalive_timeout  65;

    #gzip  on;

    include /etc/nginx/conf.d/*.conf;
}
```

### 2. 七牛云图床前端配置

```nginx
server {
    listen 80;
    listen 443 ssl;
    server_name qn.chaizz.com;
    # ssl on;  # 已经被弃用
    ssl_certificate /opt/apps/nginx/cert/qn.chaizz.com_bundle.pem;
    ssl_certificate_key /opt/apps/nginx/cert/qn.chaizz.com.key;
    ssl_session_timeout 5m;
    #按照这个协议配置
    ssl_protocols TLSv1 TLSv1.1 TLSv1.2;
    #按照这个套件配置
    ssl_ciphers ECDHE-RSA-AES128-GCM-SHA256:HIGH:!aNULL:!MD5:!RC4:!DHE;
    ssl_prefer_server_ciphers on;

    root /opt/apps/web_apps/qiniu;
    index index.html;
    location / {
        try_files $uri $uri/ /index.html;
    }

    ###########设置缓存###############
    location ~ .*\.(gif|jpg|png|css|js)(.*) {
        proxy_set_header Host $host;
        proxy_cache_valid 301 30d;
        proxy_cache_valid any 5m;
        expires 10d;
        }
}
```

### 3. 七牛云图床后端配置

```nginx
upstream qn_backend {
    server 101.35.188.40:6677;
}

server {
    listen 80;
    listen 443 ssl;
    # ssl on;  # 已经被弃用
    server_name  img.chaizz.com;
    ssl_certificate  /opt/apps/nginx/cert/img.chaizz.com_bundle.pem;       # 证书公钥，这里在nginx.conf所在文件夹下面的
cert文件夹里面
    ssl_certificate_key  /opt/apps/nginx/cert/img.chaizz.com.key;  # 证书私钥
    ssl_session_timeout  5m;
    ssl_session_cache    shared:SSL:1m;
    ssl_ciphers          ECDHE-RSA-AES128-GCM-SHA256:ECDHE:ECDH:AES:HIGH:!NULL:aNULL:!MD5:!ADH:!RC4;
    ssl_protocols        TLSv1 TLSv1.1 TLSv1.2;
    ssl_prefer_server_ciphers  on;

    location / {
            # 请求转发到多个gunicorn服务器
            proxy_pass http://qn_backend;
            # 设置请求头，并将头信息传递给服务器端
            proxy_set_header Host $host;
            # 设置请求头，传递原始请求ip给 gunicorn 服务器
            proxy_set_header X-Real-IP $remote_addr;
    }
}

```

### 4. vitepress 配置

```nginx
server {
    listen 80;
    server_name ninth.games www.ninth.games;
    # 重定向到 
    return 301 https://www.ninth.games$request_uri;
}


server {

    listen 443 ssl;
    server_name  www.ninth.games;

    # SSL 配置
    ssl_certificate /opt/apps/nginx/cert/ninth.games_bundle.pem;
    ssl_certificate_key /opt/apps/nginx/cert/ninth.games.key;
    ssl_protocols TLSv1 TLSv1.1 TLSv1.2; #按照这个协议配置
    ssl_ciphers ECDHE-RSA-AES128-GCM-SHA256:HIGH:!aNULL:!MD5:!RC4:!DHE;#按照这个套件配置
    ssl_prefer_server_ciphers on;

    root /opt/apps/web_apps/vitepress;
    index index.html;

    location / {
        try_files $uri $uri/ /index.html;
    }

    ###########设置缓存###############
    location ~ .*\.(gif|jpg|png|css|js)(.*) {
        proxy_set_header Host $host;
        proxy_cache_valid 301 30d;
        proxy_cache_valid any 5m;
        expires 10d;
        }
}
```

### 5. mqtt emqx 前端配置

```nginx
server {
        listen  80;
        server_name  emqx.ninth.club;

        location /dashboard {
            proxy_pass  http://101.35.188.40:18083;
            # 设置代理请求头
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Real-PORT $remote_port;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        }
}
```

### 6. fastapi-todolist 配置

```nginx
server {
    listen 80;
    server_name todo.ninth.games;

    location / {
        proxy_pass http://101.35.188.40:3033;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
    }

    root /fastapi-todolist;
    location /static {
        alias /static;
    }


}
```

## 三、EMQX

创建持久化目录

```shell
mkdir -p ./emqx/data   ./emqx/log  && cd ./emqx
```



```shell
docker run -d --name emqx \
	--restart=always \
    -p 1883:1883 -p 8083:8083 \
    -p 8084:8084 -p 8883:8883 \
    -p 18083:18083 \
    -v ./data:/opt/emqx/data \
    -v ./log:/opt/emqx/log \
    emqx/emqx:latest
```

## 四、RabbitMQ

```shel
mkdir -p ./rabbitmq/data   ./rabbitmq/log  ./rabbitmq/conf  && cd ./rabbitmq
```

```shell
docker run -d --name rabbitmq \
	--restart=always \
    -e RABBITMQ_DEFAULT_USER=admin \
    -e RABBITMQ_DEFAULT_PASS=admin \
    -p 15672:15672 -p 5672:5672 \
    -v ./data:/etc/lib/rabbitmq \
    -v ./log:/var/log/rabbitmq \
	-v ./conf:/var/rabbitmq/conf \
    rabbitmq:management
```

## 五、Sentry

1、获取sentry 配置文件

```shell
docker run -it --rm -e SENTRY_SECRET_KEY='izb)os5w=n#8w-@ujxnu5m!bbwxe+r)9b@h!p-s+wsdo=a5%j%' --link sentry-postgres:postgres --link sentry-redis:redis sentry upgrade
```

```shell
docker run -d --name my-sentry  -p 8080:9000 -e SENTRY_SECRET_KEY='izb)os5w=n#8w-@ujxnu5m!bbwxe+r)9b@h!p-s+wsdo=a5%j%' --link sentry-redis:redis --link sentry-postgres:postgres sentry
```

```
docker run -d --name sentry-cron -e SENTRY_SECRET_KEY='izb)os5w=n#8w-@ujxnu5m!bbwxe+r)9b@h!p-s+wsdo=a5%j%' --link sentry-postgres:postgres --link sentry-redis:redis sentry run cron
```

```
docker run -d --name sentry-worker-1 -e SENTRY_SECRET_KEY='izb)os5w=n#8w-@ujxnu5m!bbwxe+r)9b@h!p-s+wsdo=a5%j%' --link sentry-postgres:postgres --link sentry-redis:redis sentry run worker
```

## 六、Grafana

```shell
docker run -d \
  --restart=always \
  -p 3000:3000 \
  --name=grafana \
  -e "GF_INSTALL_PLUGINS=grafana-clock-panel,grafana-simple-json-datasource" \
  grafana/grafana-enterprise
```

## 七、Redis

```shell
mkdir -p ./redis/data ./redis/conf  && cd redis
```



```shell
docker run  -d --restart=always \
	--log-opt max-size=100m \
    --log-opt max-file=2 \
    -p 9736:6379 --name myredis \
    -v ./conf/redis.conf.conf:/etc/redis/redis.conf \
    -v ./data:/data \
    redis redis-server /etc/redis/redis.conf \
    --appendonly yes  --requirepass 123456
```

redis 配置文件

```shell
# redis.conf

bind 0.0.0.0

protected-mode no
# Redis 服务器的端口号（默认：6379）
port 9736
tcp-backlog 511
requirepass 123456
timeout 0
tcp-keepalive 300
daemonize no
supervised no
pidfile /var/run/redis_6379.pid
loglevel notice
logfile ""
databases 30
always-show-logo yes
save 900 1
save 300 10
save 60 10000
stop-writes-on-bgsave-error yes
rdbcompression yes
rdbchecksum yes
dbfilename dump.rdb
dir ./
replica-serve-stale-data yes
replica-read-only yes
repl-diskless-sync no
repl-disable-tcp-nodelay no
replica-priority 100
lazyfree-lazy-eviction no
lazyfree-lazy-expire no
lazyfree-lazy-server-del no
replica-lazy-flush no
appendonly yes
appendfilename "appendonly.aof"
no-appendfsync-on-rewrite no
auto-aof-rewrite-percentage 100
auto-aof-rewrite-min-size 64mb
aof-load-truncated yes
aof-use-rdb-preamble yes
lua-time-limit 5000
slowlog-max-len 128
notify-keyspace-events ""
hash-max-ziplist-entries 512
hash-max-ziplist-value 64
list-max-ziplist-size -2
list-compress-depth 0
set-max-intset-entries 512
zset-max-ziplist-entries 128
zset-max-ziplist-value 64
hll-sparse-max-bytes 3000
stream-node-max-bytes 4096
stream-node-max-entries 100
activerehashing yes
hz 10
dynamic-hz yes
aof-rewrite-incremental-fsync yes
rdb-save-incremental-fsync yes

```

## 八、MongoDB

创建存储目录

````she
mkdir  ./datadir  ./config   ./logs
````



```dockerfile
# docker-compose.yml

version: '3'
services:
    mongo:
        image: mongo:latest
        container_name: mongo
        restart: always
        ports:
            - "27017:27017"
        volumes:
            - ./mongo/datadir:/data/db
            - ./mongo/logs:/data/logs
            - ./mongo/config:/etc/mongo
        environment:
            MONGO_INITDB_ROOT_USERNAME: admin
            MONGO_INITDB_ROOT_PASSWORD: 123456
            TZ: Asia/Shanghai
        command:
        	# 设置WiredTiger缓存大小限制
            - --wiredTigerCacheSizeGB
            - '1.5'
            # 如果有配置文件，则启动方式加上 --config
            - --config
            - /etc/mongo/mongod.conf
```





## 九、MySQL

```shell
mkdir -p ./mysql/data ./mysql/conf && cd ./mysql
```



```dockerfile
# docker-compose.yml

version: '3'
services:
  mysql:
    restart: always
    image: mysql:latest
    # 选择镜像，这里用的是mysql8，低版本mysql需要修改配置文件映射地址
    container_name: my_mysql
    volumes:
      # 挂载数据目录
      - ./data:/var/lib/mysql
      # 挂载 my.cnf 配置文件
      - ./conf/my.cnf:/etc/mysql/my.cnf
    command: --default-authentication-plugin=mysql_native_password
    environment:
      - "MYSQL_ROOT_PASSWORD=123456"
      # 设置密码
      - "TZ=Asia/Shanghai"
      # 设置时区
    ports:
      - 3306:3306
    # 设置端口
```

MySQL配置文件

```shell
# Copyright (c) 2017, Oracle and/or its affiliates. All rights reserved.
#
# This program is free software; you can redistribute it and/or modify
# it under the terms of the GNU General Public License as published by
# the Free Software Foundation; version 2 of the License.
#
# This program is distributed in the hope that it will be useful,
# but WITHOUT ANY WARRANTY; without even the implied warranty of
# MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
# GNU General Public License for more details.
#
# You should have received a copy of the GNU General Public License
# along with this program; if not, write to the Free Software
# Foundation, Inc., 51 Franklin St, Fifth Floor, Boston, MA  02110-1301 USA
#
# The MySQL  Server configuration file.
#
# For explanations see
# http://dev.mysql.com/doc/mysql/en/server-system-variables.html

[mysqld]
pid-file        = /var/run/mysqld/mysqld.pid
socket          = /var/run/mysqld/mysqld.sock
datadir         = /var/lib/mysql
# skip-grant-tables
# 不使用密码
#secure-file-priv=NULL
# 设置FILE权限
# Custom config should go here
!includedir /etc/mysql/conf.d/
```



mysql 设置远程访问

```shell
docker exec -it 容器ID bash

mysql -uroot -p 密码

mysql>  ALTER USER 'root'@'%' IDENTIFIED WITH mysql_native_password BY '密码';

mysql> flush privileges;
```

## 十、StarRocks

```shell
docker run -it \
-v /opt/starRocks/.m2:/root/.m2 \
-v /opt/starRocks/starrocks:/root/starrocks \
--name starrocks \
-d starrocks/dev-env:main
```

```shell
docker exec -it {container-name} /bin/bash

cd /root/starrocks

./build.sh
```

## 十一、RocketMQ

### 方式一：单独部署

创建NameServer

#### 1. 创建NameSrv数据存储路径

```
mkdir -p  ./rocketmq/data/namesrv/logs   ./rocketmq/data/namesrv/store && cd ./rocketmq
```

#### 2. 运行namesrv容器

```shell
docker run -d \
--restart=always \
--name rmqnamesrv \
-p 9876:9876 \
-v ./data/namesrv/logs:/root/logs \
-v ./data/namesrv/store:/root/store \
-e "MAX_POSSIBLE_HEAP=100000000" \
rocketmqinc/rocketmq \
sh mqnamesrv
```

#### 3. 创建borker节点数据存储路径

```
mkdir -p  ./rocketmq/data/broker/logs   ./rocketmq/data/broker/store ./rocketmq/conf && cd ./rocketmq
```

#### 4. 创建borker配置文件

```yaml
# ./rocketmq/conf/broker.conf

# 所属集群名称，如果节点较多可以配置多个
brokerClusterName = DefaultCluster
#broker名称，master和slave使用相同的名称，表明他们的主从关系
brokerName = broker-a
#0表示Master，大于0表示不同的slave
brokerId = 0
#表示几点做消息删除动作，默认是凌晨4点
deleteWhen = 04
#在磁盘上保留消息的时长，单位是小时
fileReservedTime = 48
#有三个值：SYNC_MASTER，ASYNC_MASTER，SLAVE；同步和异步表示Master和Slave之间同步数据的机制；
brokerRole = ASYNC_MASTER
#刷盘策略，取值为：ASYNC_FLUSH，SYNC_FLUSH表示同步刷盘和异步刷盘；SYNC_FLUSH消息写入磁盘后才返回成功状态，ASYNC_FLUSH不需要；
flushDiskType = ASYNC_FLUSH
# 设置broker节点所在服务器的ip地址
brokerIP1 = 101.35.188.40
# 磁盘使用达到95%之后,生产者再写入消息会报错 CODE: 14 DESC: service not available now, maybe disk full
diskMaxUsedSpaceRatio=95
```

#### 5. 创建borker容器

```shell
docker run -d  \
--restart=always \
--name rmqbroker \
--link rmqnamesrv:namesrv \
-p 10911:10911 \
-p 10909:10909 \
-v  ./data/broker/logs:/root/logs \
-v  ./data/broker/store:/root/store \
-v ./conf/broker.conf:/opt/rocketmq-4.4.0/conf/broker.conf \
-e "NAMESRV_ADDR=namesrv:9876" \
-e "MAX_POSSIBLE_HEAP=200000000" \
rocketmqinc/rocketmq \
sh mqbroker -c /opt/rocketmq-4.4.0/conf/broker.conf 
```

#### 6. 创建Rockermq-console服务

```
docker run -d \
--restart=always \
--name rmqadmin \
-e "JAVA_OPTS=-Drocketmq.namesrv.addr=你的rockertmq主机IP:9876 \
-Dcom.rocketmq.sendMessageWithVIPChannel=false" \
-p 9999:8080 \
pangliang/rocketmq-console-ng
```

### 方式二：compose 方式部署

需要创建对应的配置文件

#### 1. docker-compose.yml

```yaml
version: '3.3'
services:
  namesrv:
    image: apache/rocketmq:4.9.4
    container_name: rmqnamesrv
    ports:
      - 9876:9876
    environment:
      JAVA_OPT_EXT: "-server -Xms512m -Xmx512m"
    volumes:
      - /opt/rocketmq/namesrv/logs:/home/rocketmq/logs
    command: sh mqnamesrv
    restart: always
  broker1:
    image: apache/rocketmq:4.9.4
    container_name: rmqbroker
    links:
      - namesrv
    ports:
      - 10909:10909
      - 10911:10911
      - 10912:10912
    environment:
      NAMESRV_ADDR: namesrv:9876
      JAVA_OPT_EXT: "-server -Xms512m -Xmx512m"
    volumes:
      - /opt/rocketmq/broker/logs:/home/rocketmq/logs
      - /opt/rocketmq/broker/store:/home/rocketmq/store
      - /opt/rocketmq/broker/conf/broker.conf:/opt/rocketmq-4.9.4/conf/broker.conf
    command: sh mqbroker -c /opt/rocketmq-4.9.4/conf/broker.conf
    restart: always
  dashbord:
    image: apacherocketmq/rocketmq-dashboard:1.0.0
    ports:
      - 9999:9999
    environment:
      JAVA_OPTS: "-Drocketmq.namesrv.addr=namesrv:9876"
    restart: always
```

