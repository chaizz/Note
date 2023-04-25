---
title: MySQL索引问题
author: chaizz
date: 2021-11-18 20:28:17
tags: MySQL
categories: MySQL
photo: ["https://origin.chaizz.com/tc/Snipaste_2021-10-08_22-12-28.png"]
---

​     

<!--more-->

针对以上索引问题，首先要知道什么是主键索引、非主键索引、聚簇索引、非聚簇索引。

主键索引：即MySQL的索引，如果没有主键那么MySQl会自动在表中挑选一个唯一且非空的字段来当做主键索引，如果没有的话MySQL内部自己会创建一个ROW_ID来当做主键，也会建立主键索引。主键索引的叶子结点存储的是整行的数据。

非主键索引：即非主键以外的列建立的索引。非主键索引存储的是主键索引的值。

### 什么是索引回表？

索引回表指的就是在查询某一列数据是判断条件为非主键索引，name查到这条复合条件的所有记录就需要在根据非主键索引获得的主键索引的值，在取主键索引的B+树中在此查询一次才能获取到全部的数据。例如：

```mysql
# ID 是主键索引 ，只需要一次查询就可以获取符合条件的全部记录。
select * from ex_table where ID=1;
```



```mysql
# n 是非主键索引，查询到的结果是符合条件的主键索引的ID，所以还需要早根据主键的ID,再在主键索引的B+树上查询一次
select * from ex_table where n = 5;
```

以上情况就是索引回表。



### 什么是索引覆盖？

如果执行的语句是 :

```mysql
select idfrom T where n between 1 and 10;
```

现在的SQL只需要得到ID 的值，而 ID 的值已经在 n 索引的B+树上了，因此可以直接获得查询结果，不需要回表。也就是说，在这个查询里面，索引 n已经“覆盖了”我们的查询需求，我们称为覆盖索引。



### 什么是索引下推？

索引下推（index condition pushdown ）简称ICP，在MySQL5.6的版本上推出，用于优化查询，默认是开启的，可以通过以下的命令关闭：

```mysql
set optimizer_switch = 'index_condition_pushdown=off';
```

当使用索引下推时如果存在某些被索引的列的判断条件时，MySQL服务器将这一部分判断条件传递给存储引擎，然后由存储引擎通过判断索引是否符合MySQL服务器传递的条件，只有当索引符合条件时才会将数据检索出来返回给MySQL服务器 。

索引下推的好处：

**索引条件下推优化可以减少存储引擎查询基础表的次数，也可以减少MySQL服务器从存储引擎接收数据的次数。**



假如有以下MySQL

```mysql
# 索引值为 name age 为组合索引。

select * from user where name like '张%' and age = 20;
```



在关闭索引下推的时候，InnoDB引擎会根据只name找到复合条件的索引字段，如下图中的左边绿色，然后就将数据返回给MySQL服务器，由MySQL服务器去判断其他的符合条件的数据。MySQL服务器会拿着查到的ID：1、2  在进行回表查询。

![](https://origin.chaizz.com/161026e0486011ec9d7c5254006b8f1d.png)

在使用索引下推的时候，InnoDB会直接找出符合索引条件的字段的ID，将符合条件的结果发送给MySQL服务器，这个过程只需要回表一次。如下图。

![](https://origin.chaizz.com/a47dc42c486b11ec9d7c5254006b8f1d.png)



