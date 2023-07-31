---
title: 使用pip安装mysqlclient时出现问题汇总
author: chaizz
date: 2021-02-02 16:04:56
tags: 
  - mysqlclient
  - pip
categories:
  - pip
photos: ["https://origin.chaizz.combdf5bb7222ee11edb23e0242ac190002.jpg"]
cover: "https://origin.chaizz.combdf5bb7222ee11edb23e0242ac190002.jpg"
---



# 使用pip安装mysqlclient时出现问题汇总

## 环境

- System: WSL Ubuntu 22.04 LTS

- Python Version： 3.8

- Pip Version: 23.2.1

- 待安装的mysqlclient Version: 2.2.0



## 问题一、OSError: mysql_config not found

![image-20230731172048442](C:\Users\LHKJ0\AppData\Roaming\Typora\typora-user-images\image-20230731172048442.png)

解决办法：

```shell
apt install libmysqlclient-dev
```

## 问题二、 error: command 'gcc' failed: No such file or directory

![image-20230731171601684](https://origin.chaizz.com/tc/image-20230731171601684.png)

解决办法：

```shell
apt install gcc
```



## 问题三、Exception: Can not find valid pkg-config name

![image-20230731171818939](https://origin.chaizz.com/tc/image-20230731171818939.png)

查找资料地址：[github issues](https://github.com/PyMySQL/mysqlclient/issues/620)

解决办法：

```shell
apt install pkg-config
```

