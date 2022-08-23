---
title: Navicat连接Oracle数据库
author: chaizz
date: 2021-05-09 16:04:46
categories: Oracle
tags: Oracle
photo: ["https://tc.chaizz.com/2782ef0022f011edb23e0242ac190002.png"]
---

​    

<!--more-->

Navicat连接Oracle出现错误信息 ：

```shell
oracle library is not loaded
```

Navicat版本 ：12.1.11

Navicat 连接Oracle数据库需要配下载Oracle 官方提供的插件 ：[instantclient_19_9](https://www.oracle.com/database/technologies/instant-client/downloads.html)，选择自己的系统（我的是windows），将此文件下载解压后放在Navicat安装文件路径下。

然后打开Navicat 的 ：工具->选项->环境->OCI 如下图所示。

![](https://tc.chaizz.com/3b0b7af0e15211eb9d7c5254006b8f1d.png)

将instantclient_19_9 路径下的oci.dll 文件路径复制到此路径下。

接下来重启Navicat 即可。

