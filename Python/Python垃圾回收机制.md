---
title: Python垃圾回收机制
author: chaizz
date: 2021-11-15 21:35:17
tags: Python
categories: Python
---

Python 的垃圾回收机制是有三种形式来实现的。

- 引用计数
- 标记清除
- 分代回收



前提：在Python中创建的对象都会放在一个**环状双向链表**（refchain）中。



引用计数



