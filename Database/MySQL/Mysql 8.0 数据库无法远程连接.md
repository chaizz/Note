---
title: Mysql 8.0 连接数据库错误
date: 2021-2-1 14:45:39
authoer: chaizz
tags: Mysql、后端
---

####  Mysql 8.0 连接数据库出现 The user specified as a definer (‘mysql.infoschema‘@‘localhost‘) does not exist

<!--more-->

解决办法：

1. 重新创建该用户（mysql.infoschema）：

   ```sql
   create user 'mysql.infoschema' @ '%' identified by 'password ';
   ```

2. 给用户赋予权限

   ```sql
   grant all privileges on *.* to 'mysql.infoschema'@'%';
   ```

3. 刷新数据库

   ```sql
   flush privileges;
   ```

   