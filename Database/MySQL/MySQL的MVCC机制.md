---
title: MySQLMVCC机制
author: chaizz
date: 2021-10-8 22:10:00
tags: MySQL
categories: MySQL
photo: ["https://origin.chaizz.com/tc/Snipaste_2021-10-08_22-12-28.png"]
---

​       

<!--more-->

> 数据库事务的四大特性：
>
> 原子性：atomicity 是通过undo log 来实现的。
>
> 一致性：consistency  原子性、隔离性、持久性来共同保持一致性。
>
> 隔离性：isolation 通过加锁以及MVCC来实现的。
>
> 持久性：durability 是通过redo log 实现的。



# 什么是MVCC？

MVCC（Multi - Version - Concurrency - Control）全称是**多版本并发控制**。主要是为了提高数据库的并发性能。MVCC只在**读已提交**和**可重复读**的两个事务级别下有效。主要是用于处理读请求。



# MySQL的快照读和当前读是什么？

## 当前读

他读取数据库记录都是当前最新的版本，会对当前读取的数据进行加锁，防止其他的事务对数据修改，属于悲观锁的一种。

以下操作都是当前读：

```mysql
select lock in share mode # (共享锁)

select for update  #(排他锁)

update # (排他锁)

insert #(排它锁)

delete  #(排它锁)

# 串行化事务隔离级别
```



## 快照读

快照读是基于和 undo log 来实现的，适用于简单 select 语句。           

首先MySQL一条记录里面包含三个隐藏列：**row_id**、**trx_id**、**roll_pointer**以及**ReadView**。

- row_id ：MySQL的B+树索引特性要求每个表必须要有一个主键。如果没有设置的话，会自动寻找第一个不包含NULL的唯一索引列作为主键。如果还是找不到，就会在这个DB_ROW_ID上自动生成一个唯一值，以此来当作主键。
- trx_id：自增的事务ID，记录的是当前事务在做INSERT或UPDATE语句操作时的事务ID（DELETE语句被当做是UPDATE语句的特殊情况，后面会进行说明）
- roll_pointer：回滚指针，通过它可以将不同的版本串联起来，形成版本链。相当于链表的next指针。



### 一张图理解undo log

![](https://origin.chaizz.com/tc/Snipaste_2021-10-08_22-43-53.png)

 

以上图中最新的记录name为赵六，他指向前一个版本name为王五，依次类推，王五的上一个版本是李四，李四的上一版本是张三。

### ReadView

**ReadView的作用？**

就是根据事务ID查找对应的数据。那些事务应该读取到那些数据。

![](https://origin.chaizz.com/tc/Snipaste_2021-10-08_22-56-42.png)



m_ids ：当前MySQL中所有的**活跃**（**指未提交的**）的事务的ID。

min_trx_id：表示m_ids 中最小的事务ID。、

max_trx_id：表示生成ReadView时系统应该分配给下一个事物的ID。、

creator_trx_id：生成该ReadView的ID。

**ReadView如何判断哪个版本可用？**

- trx_id == creator_trx_id    如果事务ID等于当前生成ReadView的vreator_trx_id ，那么可以访问这个版本。
- trx_id  < mix_trx_id 事务ID小于当前ReadView中的最小的活跃事务的ID，说明该事务是已经提交的了事务。那么可以访问。
- trx_id  > max_trx_id 事务ID大于当前ReadView中的要分配的事务的ID，说明max_trx_id  是创建ReadView之后生成的ID，已经超出版本链的事务的ID，所以无法读取。
- min_trx_id  <=  trx_id  <= max_trx_id 分两种情况：
  - ① 当trx_id   在min_trx_id   中，说明是活跃的事务的ID，未提交的事务，那么不能访问当前版本。
  - ②当trx_id  不在min_trx_id   中，说明不是活跃的事务的ID，而且小于系统下一个事务的ID，那么可以访问当前版本。



## MVCC如何实现RR （可重复读）和RC（读已提交）的？

**实现RC**

RC生成ReadView的时间是每次select查询的时候就会生成一个ReadView，他是以每个select查询为单位的，比如一个事务中有两个select语句，那么这两个select就会生成两个ReadView，所以在一个事务中就可以读取到其他事务已经提交的数据，就会产生不可重复读的问题。



**实现RR**

RR 生成ReadView 是根据事务来生成，他是以每个事务为单位生成的。在一个事务中不管有多少查询语句，查询的ReadView还是同一个，所以只能读取到当前版本的数据，就可以解决不可重复读的问题。



InndDB 解决幻读：是通过间隙锁+锁住本身的数据（next-key），间隙锁锁住一段范围，所以其他事务无法对这段范围的数据进行插入删除等操作，所以就不存在幻读的问题。







