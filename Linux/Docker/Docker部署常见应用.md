# 登陆Docker远程仓库

```
username:admin
password:Harbor12345
```

查看 Redis 连接

```
redis-cli  -a laihui  CLIENT LIST | awk '{printf "%-32s| %-16s| %-16s| %-16s| %-16s| %-16s| %s\n", $2,$5,$6,$7,$12,$16,$18}'
```

# 部署 算法服务

```shell
docker build -t python-dev:v1 .
docker tag python-dev:v1 docker-registry.laihui.com/laihui/python-dev:latest
docker push  docker-registry.laihui.com/laihui/python-dev:latest
```

# 制作基础算法服务镜像

```
docker build -t python-base:v1 .
docker tag python-base:v1 docker-registry.laihui.com/laihui/python-base:latest
docker push  docker-registry.laihui.com/laihui/python-base:latest
```

# 部署InfluxDB

```shell
docker run -d \
    --name influxdb \
    -p 8086:8086 \
    --volume $PWD:/var/lib/influxdb2 \
    -v $PWD/config.yml:/etc/influxdb2/config.yml \
    influxdb:2.1.1
```

# 部署RabbitMQ

```shell
docker run -d --name Myrabbitmq -e RABBITMQ_DEFAULT_USER=admin -e RABBITMQ_DEFAULT_PASS=admin -p 15672:15672 -p 5672:5672 rabbitmq:management
```

# 部署Sentry

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

# 部署 grafana

```shell
docker run -d \
  -p 3000:3000 \
  --name=grafana \
  -e "GF_INSTALL_PLUGINS=grafana-clock-panel,grafana-simple-json-datasource" \
  grafana/grafana-enterprise
```



# 部署redis

```shell
docker run --restart=always --log-opt max-size=100m --log-opt max-file=2 -p 9736:6379 --name myredis -v /opt/redis/conf/myredis.conf:/etc/redis/redis.conf -v /opt/redis/data:/data -d redis redis-server /etc/redis/redis.conf  --appendonly yes  --requirepass 123456
```



```shell
# bind 192.168.1.100 10.0.0.1
# bind 127.0.0.1 ::1
bind 0.0.0.0

protected-mode no
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















# 部署mongoDB



# 部署Mysql

```dockerfile
version: '3'
services:
  mysql:
    restart: always
    image: mysql:latest
    # 选择镜像，这里用的是mysql8，低版本mysql需要修改配置文件映射地址
    container_name: my_mysql
    volumes:
      # 挂载数据目录
      - ./datadir:/var/lib/mysql
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





# 部署人脸识别Demo

```shell
docker build -t flask_face_recognition:latest .
docker tag flask_face_recognition:latest docker-registry.laihui.com/laihui/flask_face_recognition:latest
docker push  docker-registry.laihui.com/laihui/flask_face_recognition:latest
```

# Docker部署starRocks

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

# Docker 部署 RocketMQ

## 拉取rocketmq镜像

```
docker pull rocketmqinc/rocketmq
```

### 创建namesrv数据存储路径

```
mkdir -p  /opt/rocketmq/data/namesrv/logs   /opt/rocketmq/data/namesrv/store
```

### 构建namesrv容器

```shell
docker run -d \
--restart=always \
--name rmqnamesrv \
-p 9876:9876 \
-v /opt/rocketmq/data/namesrv/logs:/root/logs \
-v /opt/rocketmq/data/namesrv/store:/root/store \
-e "MAX_POSSIBLE_HEAP=100000000" \
rocketmqinc/rocketmq \
sh mqnamesrv
```

## 创建borker 节点

```
mkdir -p  /opt/rocketmq/data/broker/logs   /opt/rocketmq/data/broker/store /opt/rocketmq/conf
```

### borker 配置文件 ：/opt/rocketmq/conf/broker.conf

```yaml
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

### 创建borker 容器

```shell
docker run -d  \
--restart=always \
--name rmqbroker \
--link rmqnamesrv:namesrv \
-p 10911:10911 \
-p 10909:10909 \
-v  /opt/rocketmq/data/broker/logs:/root/logs \
-v  /opt/rocketmq/data/broker/store:/root/store \
-v /opt/rocketmq/conf/broker.conf:/opt/rocketmq-4.4.0/conf/broker.conf \
-e "NAMESRV_ADDR=namesrv:9876" \
-e "MAX_POSSIBLE_HEAP=200000000" \
rocketmqinc/rocketmq \
sh mqbroker -c /opt/rocketmq-4.4.0/conf/broker.conf 
```

## 创建Rockermq-console服务

### 拉取 rockermq-console 镜像

```
docker pull pangliang/rocketmq-console-ng
```

### 创建rockermq-console容器

```
docker run -d \
--restart=always \
--name rmqadmin \
-e "JAVA_OPTS=-Drocketmq.namesrv.addr=49.235.122.63:9876 \
-Dcom.rocketmq.sendMessageWithVIPChannel=false" \
-p 9999:8080 \
pangliang/rocketmq-console-ng
```

