---
title: Centos7.5源码安装postgresql-12
author: chaizz
date: 2021-02-02 15:58:26
tags: PostgreSQL
categories: PostgreSQL
photos: ["https://origin.chaizz.com/b894ccf022f211edb23e0242ac190002.png"]
---

​          

<!--more-->

### 源码安装

1. 首先安装readline 和readline-devel

   ```shll
   yum install readline 
   yum install readline-devel
   ```

虽然已经安装了readline，但是不安装readlene-devel 在执行 `./configure --prefix=/opt/postgresql` 的时候会提示，`configure: error: readline library not found`。

2. 接下来在执行 

   ```shell
   ./configure --prefix=/opt/postgresql
   make && make install
   ```

3. 将postgresql 加入环境变量，打开文件 /etc/profile  在末尾添加

   ```shell
   export PG_HOME=/opt/postgresql  #为安装postgresql的地址
   export PATH=$PG_HOME/bin:$PATH
   source /etc/profile    #更新环境变量
   ```

4. 创建postgresql 的用户以及用户组 可创建可不创建

   ```shell
   groupadd postgresql   #创建用户组
   useradd -G postgresql postgresql   #创建用户
   passwd ********    #设置密码  
   ```


5. 初始数据库

   ```shell
   su postgresql -c 'pg_ctl -D /opt/postgresdata initdb' #初始化数据库
   su postgresql   ：使用这个用户进行操作
   /opt/postgresdata：地址为存放初始化的文件路径可自定义，   这个文件的用户权限要为 postgresql可操作
   pg_ctl  ：postgresql 的控制器，可以对postgresql 进行启动停止等操作
   ```

   执行以上语句后会再  /opt/postgresdata 路径下生成一些文件

   ![](postgresql0.jpg)

| 文件            | 描述                |
| --------------- | ------------------- |
| postmaster.pid  | 首行记录了进程PID   |
| serverlog       | 数据库日志          |
| postgresql.conf | 主配置文件(可做定制 |
| pg_hba.conf     | 鉴权相关文件        |
| PG_VERSION      | 当前主版本号        |

6. 启动数据库

   ```shell
   su postgresql -c 'pg_ctl start -D /opt/postgredata -l serverlog'
   ```

   

7. 设置远程访问,在 pg_hba.conf文文件末尾加上：

   ```shell
   host all all 0.0.0.0/0 md5
   ```

8. 为了让 postgresql用户可以远程访问，可以通过 psql 设置密码：

   ```
   alter user postgresql with password '*********';
   ```



9. 开启远程访问，默认情况下 postgresql 仅仅监听本机的端口，需要编辑 **/opt/postgredata/ postgresql.conf** 文件开启远程IP的访问

   ```
   listen_addresses = '*'
   ```

10. 常用命令

    ```shell
    #如果需要定制端口，可以执行脚本：
    postgres -p 5430 -D /opt/postgredata >serverlog 2>&1 &
    #检查进程是否存活：
    netstat -nlp |grep `head -1 /opt/postgredata/postmaster.pid`
    #停止数据库进程
    kill -INT `head -1 /opt/postgredata/postmaster.pid`
    ```

11. 配置自启动

    找到源码目录中 ***contrib/start-scripts/linux\***脚本文件，拷贝为 **/etc/init.d/postgressql**。修改内容如下：

    ```shell
    # 程序所在目录
    prefix=/opt/postgresql
    
    # 数据目录
    PGDATA="/opt/postgredata"
    
    # 运行用户
    PGUSER=postgresql
    
    # 日志文件
    PGLOG="$PGDATA/serverlog"
    ```

    设置执行权限

    ```shell
    chmod +x /etc/init.d/postgressql		
    ```
    
    此后，执行以下命令可以方便的启停服务

    ```shell
    //手动启动服务
    service postgressql start
    
    //查看服务状态
    service postgressql status
    
    //手动停止服务
    service postgressql stop
    ```
    
12.  设置开机启动 ,执行以下命令：

    ```sheLl
    chkconfig --add postgressql
    ```

    

### yum安装

 [官网安装教程地址](https://www.postgresql.org/download/linux/redhat/)

安装完成会自动创建一个用户为：postgres  

首先使用管理员用户修改密码，然后登录以后，运行psql 即可使用，

允许远程访问，yum安装的ppostgresql 的配置文件在`/var/lib/pgsql/12/data` 目录下

修改配置文件 `postgresql.conf` 将 `listen_addresses = 'localhost' `改为 `listen_addresses = '*' `

然后在修改 `pg_hba_conf` 在最后一行添加 `host all  all  0.0.0.0/0  md5`  允许所有IIP访问， 如果限定IP,将0.0.0.0 改为指定IP。

重启命令postgresql  ：`systemctl restart postgresql-12`



参考材料：[ https://www.cnblogs.com/littleatp/p/10542137.html ]()