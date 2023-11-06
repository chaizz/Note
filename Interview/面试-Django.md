---
​---
title: 面试-Django
author: chaizz
date: 2021-11-15 21:28:48
tags: 面试
categories:
    - 面试
    - Django
photos: ["https://origin.chaizz.com/037cabda46cf11ec9d7c5254006b8f1d.png"]
cover: "https://origin.chaizz.com/037cabda46cf11ec9d7c5254006b8f1d.png"
---

# Django





## Django基础
### 整体结构

如何理解设计模式中的MVC模式，你平时怎么使用这种模式？
如何理解Django中的MTV模型？
介绍一下Django中你熟悉的模块及其作用。
如何看待Django自带的admin，并说说你的使用经验。
如何理解WSGI的作用？
如何自己实现WSGI协议？
为什么正式部署不要开启DEBUG=True?
### Model层

如何理解Django migrations的作用？
是否有过手动编辑migrations文件的经历？原因是什么？有哪些需要注意的？
介绍一下ORM的概念？
介绍一下ORM下的N+1问题、发生的原因以及解决方案。
介绍一下Django中Model的作用。
Model的Meta属性类有哪些可配置项？其作用是什么？日常怎么优化它？
介绍一下Queryset的作用以及你常用的QuerySet优化措施？
介绍一下Pagination的用法。
介绍一下Model中Field的作用。
如何定制Manager？什么场景下需要定制？
原生SQL的效率跟ORM的效率是否进行过对比？结果如何？如何理解这种差异？
Django内置的权限逻辑以及其粒度？
### View层

Django的funtion view和class-based view的差别及适用场景。
如何给class-based view添加login required装饰器？
middleware在Django系统中的作用。
settings中默认配置的MIDDLEWARES有哪些？它们的作用分别是什么？是否可以移除？
Django系统如何判断用户是否为登录用户？
对于无cookie的浏览器，如何实现用户登录？
Django中的request和HttpResponse的作用是什么？
如何处理图片上传的逻辑以及展示逻辑？
介绍一下用过的Django缓存粒度？
### Form层

介绍一下Django中Form的作用。
Form中的Field跟Model中的Field有何关联？
如何在Form层实现对某个字段的校验？
Template层

如何理解Django模板对设计师友好的说法？
日常开发中如何规划Django的模板继承和include？
常用的标签(tag)和过滤器(filter)有哪些？
在模板中如何处理静态文件？
在模板中如何处理系统内定义的URL？
如何自定义标签和过滤器？
### Django进阶
如何排查Django项目的性能问题？
如何部署Django项目？不同的部署方式之间的差别有哪些？
部署时如何处理项目中的静态文件？
如何实现自定义的登录认证逻辑？
如何理解Django中的Model、Form、ModelForm和Field、widget之间的关系？
paginator的原理什么？如何自己实现分页逻辑？
Model中的Field的作用是什么？
什么是SQL注入？ORM又是如何解决这个问题的？
CSRF的全称是什么？Django是如何解决这个问题的？
XSS攻击是什么？在开发时应该如何避免这种攻击？
signal的作用以及实现逻辑是什么？
DATABASE配置中CONN_MAX_AGE参数的作用以及使用场景？
CONN_MAX_AGE的实现逻辑是什么？
用Django内置的User模型创建用户时，是否可以直接用User(username='the5fire', password='the5fire').save()？
上面的创建方式有什么问题？应该如何处理用户密码？
使用django-rest-framework如何实现用户认证登录逻辑？
session模块在Django中的作用是什么？
如何自定义Django中的权限粒度，实现自己的权限逻辑？
如何捕获线上系统的异常？
如何分析某个接口响应时间过长的问题？假设响应时间为2S，一次请求会涉及哪些数据库和缓存查询？