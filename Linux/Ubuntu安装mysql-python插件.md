---
title: Ubuntu安装mysql-python插件
author: chaizz
date: 2021-02-02 16:04:56
tags: MySQL-Python
categories: MySQL-Python
photos: ["https://tc.chaizz.combdf5bb7222ee11edb23e0242ac190002.jpg"]
---

​      

<!--more-->

> 运行FLASK 提示 ：ERROR:flask.app:No module named MySQLdb

### 安装 mysql-python

```shell
pip install mysql-python
```

### 遇到问题

![snipaste_20190917_205355](snipaste_20190917_205355.jpg)

### 接下来安装依赖包

```shell
sudo apt-get install libmysqlclient-dev
```

### 如果还是不行安装依赖

```shell
sudo apt-get install python-dev
sudo apt-get install python-MySQLdb
```



