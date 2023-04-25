---
title: MySQL性能优化总结
author: chaizz
date: 2021-11-13 17:50:17
tags: MySQL
categories: MySQL
photo: ["https://origin.chaizz.com/tc/Snipaste_2021-10-08_22-12-28.png"]
---

​                

<!--more-->

# Mysql 性能优化总结





![图片](https://mmbiz.qpic.cn/mmbiz_png/sHw3tUJQYqv8YPTrg9lpFjt5WmnTicwibMbFhqkh287yQXUbdAeBficySb7mroxZiaeWbQdZLpF5T8mCNUC3OibbxJw/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

## 1 客户端方向：

  ### 1.1、客户端使用连接池，



## 2 服务器端方向：

- 服务端连接数改大。
- 使用缓存组件：例如redis等。 
- 基于主从复制的读写分离。
- 垂直分库，水平分库分表。
- 开启慢查询日志
- SQL和索引的优化

 

需要在mysql的配置文件中去开启

查看是否开启：`show variables like 'slow_query%';`

查看多久算是慢查询语句：`show variables like 'long_query_time';`



![图片](https://mmbiz.qpic.cn/mmbiz_png/sHw3tUJQYqv8YPTrg9lpFjt5WmnTicwibMibRnvc7Dw7ZG6hx9hvAtQ5jYicsAAibaQDR7qvRsuRvZj4U09NudwYXoA/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

## 3 多张表联合查询，是先查小表还是大表？

小表驱动大表的思想：两表查询结果的笛卡尔乘积，越小越好，它的中间结果就会小一些，减少内存的消耗。所以在连接查询的时候左表建议是小表。