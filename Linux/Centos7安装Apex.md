---
title: Centos7安装Apex
author: chaizz
date: 2021-04-24 10:15:22
tags: ORACLE-APEX
categories: ORACLE-APEX
photos: ["https://origin.chaizz.com/city-4679928_640.png"]
---

封面图片由<a href="https://pixabay.com/zh/users/khirulislam898-12875647/?utm_source=link-attribution&amp;utm_medium=referral&amp;utm_campaign=image&amp;utm_content=4679928">MD KHIRUL ISLAM</a>在<a href="https://pixabay.com/zh/?utm_source=link-attribution&amp;utm_medium=referral&amp;utm_campaign=image&amp;utm_content=4679928">Pixabay</a>上发布。

<!--more-->

### 安装前提

> 系统：Centos 7.6
>
> 所需 文件：java jdk  、 tomcat9 、Oracle 19c、   Ords 20.4 、 apex 20.2

安装高版本的JDK和Tomcat会导致出现下面这个问题。建议选择低版本进行安装，低版本和高版本安装大致方法一直，或者采用以下脚本执行。

![](https://origin.chaizz.com/%E6%89%93%E5%BC%80ords%E5%87%BA%E7%8E%B0%E9%97%AE%E9%A2%98.png)

### 一、脚本文件方式安装JDK和tomcat

该脚本文件来自 ：https://note.youdao.com/ynoteshare1/index.html?id=5a6332d51b4e56dff1cdc98af238b0f0&type=note

```shell
#!/bin/bash
#用于安装tomcat
JAVA_VER=271
JAVA_DIR=/usr/local/jdk
TOMCAT_VER=9.0.45
Pro=tomcat
CATALINA_HOME=/usr/local/$Pro
[ ! -d /software/ ] && mkdir /software
#安装依赖包
install_java(){
    cd /software
    #卸载openjdk
    m=`rpm -qa |grep openjdk |wc -l`
    [ $m -ne 0 ] && rpm -qa |grep openjdk |xargs rpm -e
    #安装java
    if [ ! -f jdk-8u$JAVA_VER-linux-x64.tar.gz ]
    then
        echo -e "\033[31m请手动下载jdk到/software\033[0m"
        exit 1
    else
        tar xf jdk-8u$JAVA_VER-linux-x64.tar.gz
        [ ! -d $JAVA_DIR ] && mv jdk1.8.0_$JAVA_VER $JAVA_DIR
        n=`grep "JAVA_HOME=$JAVA_DIR" /etc/profile |wc -l`
        if [ $n -eq 0 ]
        then
            echo "JAVA_HOME=$JAVA_DIR" >> /etc/profile
            echo 'PATH=$PATH:$JAVA_HOME/bin:$JAVA_HOME/jre/bin' >> /etc/profile
            echo 'CLASSPATH=.:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar:$JAVA_HOME/jre/lib' >> /etc/profile
            echo 'export JAVA_HOME PATH CLASSPATH' >> /etc/profile
            source /etc/profile
        else
            source /etc/profile
        fi
    fi
    java -version
    if [ $? -eq 0 ]
    then
        echo -e "\033[36mjdk安装完成\033[0m"
    else
        echo -e "\033[31mjdk安装失败\033[0m"
        exit 1
    fi
}
#安装tomcat
install_tomcat(){
    #下载tomcat
    [ ! -f apache-tomcat-$TOMCAT_VER.tar.gz ] && wget https://mirrors.tuna.tsinghua.edu.cn/apache/tomcat/tomcat-9/v$TOMCAT_VER/bin/apache-tomcat-$TOMCAT_VER.tar.gz
    #解压
    [ ! -d apache-tomcat-$TOMCAT_VER ] && tar xf apache-tomcat-$TOMCAT_VER.tar.gz
    [ ! -d $CATALINA_HOME ] && mv apache-tomcat-$TOMCAT_VER $CATALINA_HOME
    #设置环境变量
    n=`grep "JAVA_HOME=$JAVA_DIR" $CATALINA_HOME/bin/catalina.sh |wc -l`
    if [ $n -eq 0 ]
    then
        sed -i "124a JAVA_HOME=${JAVA_DIR}" $CATALINA_HOME/bin/catalina.sh
    fi
    #配置启动脚本
    cat > /etc/systemd/system/$Pro\.service << EOF
[Unit]
Description=Apache Tomcat 9 Servlet Container
After=syslog.target network.target

[Service]
User=root
Group=root
Type=forking
LimitNOFILE=131070
Environment=CATALINA_PID=${CATALINA_HOME}/bin/tomcat.pid
Environment=CATALINA_HOME=${CATALINA_HOME}
Environment=CATALINA_BASE=${CATALINA_HOME}
ExecStart=${CATALINA_HOME}/bin/startup.sh
ExecStop=${CATALINA_HOME}/bin/shutdown.sh
Restart=on-failure

[Install]
WantedBy=multi-user.target
EOF

    #tomcat开机启动
    systemctl daemon-reload && systemctl enable $Pro

    if [ `systemctl status $Pro | grep 'running'| wc -l` -eq 0 ]
    then
        systemctl start $Pro
        if [ $? -eq 0 ]
        then
            echo -e "\033[36m$Pro安装完毕\033[0m"
        else
            echo -e "\03331m$Pro安装失败\033[0m"
            exit 1
        fi
    fi
}

install_java
install_tomcat

```



将此脚本复制到系统制定目录下，在根目录下创建/software 。并将JDK 和 tomcat 包放在/software下。

将此脚本文件设置为可执行文件

```shell
chmod 777 filename.sh
# 然后执行此文件
sh filename.sh 
```

如果显示以下错误：则表示脚本文件中存在乱码。

![](https://ae01.alicdn.com/kf/U42528946957d48ff8d31a1fd8b4a3034I.jpg)

解决办法：

```shell
# 使用 vim -b  模式打开文件
vim -b filename.sh
```

打开发现如图所示。

![](https://ae01.alicdn.com/kf/U3b6e1138b0b742eea8f46daa886d3296w.jpg)



快速处理办法

```shell
sed -i 's/\r//g' filename.sh
```

接下来再打开就可以执行了。

等待程序运行完毕即可。

### 一、安装java和tomcat环境。

1、下载java SE [文件下载地址](https://www.oracle.com/java/technologies/javase-jdk16-downloads.html)，本次选择的是java SE 16 版本。tomcat 版本10.0.5[文件下载地址](https://tomcat.apache.org/download-10.cgi)。

将下载的压缩包挤压到指定文件夹。

```shell
# 解压文件
tar -zxvf jdk-16_linux-x64_bin.tar
```

2、设置环境变量

以下是*错误的配置*：（在终端输入java -version 没有问题 但是运行tomcat 会出现提示启动成功，但是后台服务不启动。）

```shell
export JAVA_HOME=/var/oracle/jdk-16
export JRE_HOME=${JAVA_HOME}/jre
export CLASSPATH=.:${JAVA_HOME}/lib:${JRE_HOME}/lib
export PATH=${JAVA_HOME}/bin:$PATH
```

原因： 因为是使用JDK16，是新版的JDK，网上查找的答案，说是从11开始就不会自动生成jre文件了。

可用的java 环境变量配置如下。

```shell
JAVA_HOME=/var/oracle/jdk-16/
JRE_HOME=$JAVA_HOME
CLASSPATH=$JAVA_HOME/lib
PATH=$JRE_HOME/bin:$JAVA_HOME/bin:$PATH
export PATH JAVA_HOME CLASSPATH
```

将JRE_HOME的路径设置为 JAVA_HOME的路径，即可解决。但是启动tomcat的时候，日志文件会打印一些列NOTE。如下图所示。

![](https://ae01.alicdn.com/kf/U0185a411b5804effb0c78bcf086a09b2g.jpg)

此问题不影响tomcat使用，目前未解决。

3、测试是否安装成功。

```shell
# 在终端输入以下命令，测试java是否安装成功，如提示java 版本信息，代表已经安装成功。
java -version 
```

4、安装tomcat。

将tomcat 解压至指定文件夹

```shell
tar -zxvf  apache-tomcat-10.0.5.tar
mv /apache-tomcat-10.0.5  /var/oracle/tomcat
```

配置tomcat 环境变量

```shell
CATALINA_BASE=/var/oracle/tomcat
PATH=$CATALINA_BASE/bin:$PATH
export PATH CATALINA_BASE
```

tomcat 的启动命令在 /tomcat/bin/start.sh 可直接启动即可。 

```shell
./start.sh 
```

查看tomcat是否启动

```shell
ps -ef|grep tomcat
```

启动成功如下图所示

![](https://ae01.alicdn.com/kf/Ud8527b3539e34383867eff8fd2b89cb3E.jpg)

如果以上java 的环境变量设置不对的话，会导致tomcat 无法启动。虽然提示tomcat启动成功，但是后并没有该服务。

```shell
#此时终端输入验证是否启动成功
curl http://ip:8080
```

如未显示tomcat启动页面HTML，可排查防火墙，是否开启了8080端口 。

```shell
# 检查防火墙是否启动
firewall-cmd --state

# 如未开启 则开启防火墙
systemctl start firewalld

# 检查80端口 是否被开启，如开启 则提示  “8080/tcp” 
firewall-cmd --permanent --zone=public --list-ports

#如未开启 则需要开启，开启成功会提示 “success”
firewall-cmd --zone=public --add-port=8080/tcp --permanent

# 重启防火墙
firewall-cmd --reload

# 验证开启的8080端口是否生效， 如果生效则提示 “yes”
firewall-cmd --zone=public --query-port=8080/tcp
```

8080端口开放，再次访问 IP:8080  即可看到tomcat访问成功信息。 



### 二、安装oracle

1、以上Java jdk 和 tomcat 安装完毕以后安装 oracle 。

2、在根目录下创建oracle 安装文件夹

```
mkdir /var/oracle  
```

3、切换到该目录下执行以下安装命令：

```shell
#首先执行 
yum localinstall  oracle-database-preinstall-19c-1.0-1.el7.x86_64.rpm

#等待执行结束，在执行
yum localinstall oracle-database-ee-19c-1.0-1.x86_64.rpm
```

中途卡断 可重新安装：

```shell
#使用yum命令 查询更刚刚安装的包, 找到Oracle的包名
yum list installed shell
#先使用以下命令卸载已安装的包。
yum remove oracle-database-preinstall-19c-1.0-1.el7.x86_64
yum remove oracle-database-ee-19c-1.0-1.x86_64
```

4、过一段时间安装完毕成功会提示如图所示。

![](https://ae01.alicdn.com/kf/Ua0442ba6df124404871dd091810d949e2.jpg)

oracle 的安装文件地址为：/opt/oracle   默认配置文件路径为：/etc/sysconfig/oracledb_ORCLCDB-19c.conf

5、接下来初始化数据库

```shell
/etc/init.d/oracledb_ORCLCDB-19c configure
```

等待数据库初始化。过程中切记不用中断操作。初始化时间可能会很长。只要后台服务还在就说明没问题。

6、切换至Oracle 用户 修改数据库系统账户密码

```shell
su oracle 
#登录数据库
sqlplus / as sysdba
```

```sql
#修改sys 和 system 的密码

alter user sys identified by test123;

alter user system identified by test123;

shutdown immediate;

startup;

alter session set container = ORCLPDB1;

startup;
```

7、配置数据库的环境变量

```shell
vi etc/profile
#在最下面添加以下内容：
export ORACLE_HOME=/opt/oracle/product/19c/dbhome_1
export ORACLE_SID=ORCLCDB
export PATH=$PATH:/opt/oracle/product/19c/dbhome_1/bin
export NLS_LANG=American_America.AL32UTF8
```

8、启动停止oracle

```shell
/etc/init.d/oracledb_ORCLCDB-19c start

/etc/init.d/oracledb_ORCLCDB-19c stop
```



### 三、安装apex

1、解压apex的压缩包

使用oracle 用户需要将 apex保存的文件富裕oracle 权限。 

```shell
chown -R oracle:dba /var/oracle/
```

```shell
# 解压到当前文件夹下
unzip -q apex_20.2.zip 
```

2、进入oracle数据库

```shell
#1、登录数据库
sqlplus / as sysdba  
```



```sql
-- 按顺序执行以下代码
-- 2、通过 alter session 切换容器为ORCLPDB1模式。
alter session set container = ORCLPDB1;   

--3、设置系统默认表空间
@apexins.sql APEX APEX TEMP /i/   

--接下来等待安装 ，会持续一段时间....。

--4、创建 apex 实例管理员
@apxchpwd.sql   

-- 设置密码必须包含：(!"#$%&()``*+,-/:;?_) 和 至少一个大写字母。
            
--5、 修改APEX_PUBLIC_USER 用户密码
ALTER USER APEX_PUBLIC_USER ACCOUNT UNLOCK IDENTIFIED BY Test123_;

-- -6、 修改flows_files 用户密码
ALTER USER flows_files ACCOUNT UNLOCK IDENTIFIED BY Test123_;

-- 7
BEGIN
DBMS_NETWORK_ACL_ADMIN.APPEND_HOST_ACE(
host => '*',
ace => xs$ace_type(privilege_list => xs$name_list('connect'),
principal_name => 'APEX_200200',
principal_type => xs_acl.ptype_db));
END;
/

-- 8
BEGIN
DBMS_NETWORK_ACL_ADMIN.APPEND_HOST_ACE(
host => 'localhost',
ace => xs$ace_type(privilege_list => xs$name_list('connect'),
principal_name => 'APEX_200200',
principal_type => xs_acl.ptype_db));
END;
/

-- 9
@apex_rest_config.sql

-- 10       
Enter a password for the APEX_LISTENER user: Test123_

-- 11        
Enter a password for the APEX_REST_PUBLIC_USER user: Test123_
            
```



3、汉化apex 

切换到，apex/builder/zh-cn/ 目录下

```shell
#1、 登录sql
sqlplus / as sysdba
```

```sql
-- 2、通过 alter session 切换容器为ORCLPDB1模式。
alter session set container = ORCLPDB1;  

-- 3、设置CURRENT_SCHEMA 
ALTER SESSION SET CURRENT_SCHEMA = APEX_200200;

-- 4、加载sql
@load_zh-cn.sql

-- 等待一段时间即可...
```



### 四、安装ords

1、解压文件到当前目录下

2、执行ords安装

```shell
java -jar ords.war install advanced
# 接下来选择会提示输入：
```

```shell
This Oracle REST Data Services instance has not yet been configured.
Please complete the following prompts


Enter the location to store configuration data: /opt/ords/config  # 配置存储路径
Specify the database connection type to use.
Enter number for [1] Basic  [2] TNS  [3] Custom URL [1]:1    
Enter the name of the database server [localhost]:localhost  
Enter the database listen port [1521]:1520                   
Enter 1 to specify the database service name, or 2 to specify the database SID [1]:1
Enter the database service name:ORCLPDB1
Enter 1 if you want to verify/install Oracle REST Data Services schema or 2 to skip this step [1]:1
Enter the database password for ORDS_PUBLIC_USER:Test123_
Confirm password:Xisland2020
Requires to login with administrator privileges to verify Oracle REST Data Services schema.

Enter the administrator username:SYS                #oracle数据库账号
Enter the database password for SYS AS SYSDBA:Test123_
Confirm password:Test123_
Connecting to database user: SYS AS SYSDBA url: jdbc:oracle:thin:@//localhost:1521/ORCLPDB1

Retrieving information.
Enter 1 if you want to install ORDS or 2 to skip this step [1]:1
Enter the default tablespace for ORDS_METADATA [SYSAUX]:SYSAUX
Enter the temporary tablespace for ORDS_METADATA [TEMP]:TEMP
Enter the default tablespace for ORDS_PUBLIC_USER [SYSAUX]:SYSAUX
Enter the temporary tablespace for ORDS_PUBLIC_USER [TEMP]:TEMP
Enter 1 if you want to use PL/SQL Gateway or 2 to skip this step.
If using Oracle Application Express or migrating from mod_plsql then you must enter 1 [1]:1
Enter the PL/SQL Gateway database user name [APEX_PUBLIC_USER]:APEX_PUBLIC_USER
Enter the database password for APEX_PUBLIC_USER:Test123_
Confirm password:Test123_
Enter 1 to specify passwords for Application Express RESTful Services database users (APEX_LISTENER, APEX_REST_PUBLIC_USER) or 2 to skip this step [1]:1
Enter the database password for APEX_LISTENER:Test123_
Confirm password:Test123_
Enter the database password for APEX_REST_PUBLIC_USER:Test123_
Confirm password:Test123_
Enter a number to select a feature to enable:
   [1] SQL Developer Web  (Enables all features)
   [2] REST Enabled SQL
   [3] Database API
   [4] REST Enabled SQL and Database API
   [5] None
Choose [1]:1
2021-02-02T08:45:42.287Z INFO        reloaded pools: []
Installing Oracle REST Data Services version 20.4.1.r0131644
... Log file written to /root/ords_install_core_2021-02-02_164542_00395.log
... Verified database prerequisites
... Created Oracle REST Data Services proxy user
... Created Oracle REST Data Services schema
... Granted privileges to Oracle REST Data Services
... Created Oracle REST Data Services database objects
... Log file written to /root/ords_install_datamodel_2021-02-02_164559_00321.log
... Log file written to /root/ords_install_apex_2021-02-02_164600_00696.log
Completed installation for Oracle REST Data Services version 20.4.1.r0131644. Elapsed time: 00:00:19.722 

Enter 1 if you wish to start in standalone mode or 2 to exit [1]:2
```

至此ords安装配置结束。

### 五、部署到tomcat 上

1、将apex 的静态文件放到tomcat的webapps 下。 路径为你的apex 安装路径：/var/oracle/apex/images

```shell
cp -a /var/oracle/apex/images/* /var/oracle/apache-tomcat-10.0.5/webapps/apex
```

2、将ords.war 包 复制到tomcat 的web apｐｓ下。

```
cp /var/oracle/ords/ords.war /var/oracle/apache-tomcat-10.0.5/webapps/
```

### 六、使用nginx 代理

Nginx 的server配置文件为：

```shell
server {
    listen 80;
    server_name 域名;
    
    add_header Access-Control-Allow-Origin *;
    add_header Access-Control-Allow-Headers X-Requested-With;
    add_header Access-Control-Allow-Methods GET,POST,OPTIONS;
    
    location / {
        proxy_pass http://localhost:8080;
        proxy_set_header Origin "" ;
        proxy_set_header X-Forwarded-Host $host:$server_port;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
        proxy_connect_timeout 120s;
        proxy_read_timeout    120s;
        proxy_send_timeout    120s;
    }
}
```

接下来重启nginx 即可。

### 七、完成

浏览器输入域名即可打开APEX登录页面。

创建的密码相关：

- apex 创建实例 用户名：admin

- 密码：Test123_

- APEX_LISTENER  user：Test1123_

- 数据库用户sys密码：test12
- 数据库用户system密码：test123



参考文章：

> https://note.youdao.com/ynoteshare1/index.html?id=5a6332d51b4e56dff1cdc98af238b0f0&type=note

> https://blog.csdn.net/weixin_41092687/article/details/89879061

> https://blog.csdn.net/qq_39400984/article/details/98104599

> https://blog.csdn.net/xzm5708796/article/details/88344074

