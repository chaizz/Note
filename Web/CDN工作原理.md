---
title: CDN的工作原理
date: 2021-2-1 14:45:39
authoer: chaizz
tags: HTTP、前端
---

### CDN访问的两个阶段

- 1.域名解析
- 2.内容请求

<!--more-->

### 使用CDN得两种方式：

- 手工上传静态资源文件到CDN
- tongguo Tengine 把本机的静态资源开放发哦web上，CDN自动回流到Tengine。

#### 以手工上传静态文件为例：Django其用户CDN静态资源加速的步骤。

- 生成静态文件上传到阿里元OSS。
- 配置CDN域名，回源地址指向OSS Bucket，配置Referer 防盗链的白名单。
- 配置OSS Buket 的匿名可以读。
- 设置STATIC_URL， 直接指向CDN地址，同时注释掉 OssStaticStorage。