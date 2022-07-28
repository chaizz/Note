---
title: 部署Kuboard
author: 2022-7-28 09:33:02
tags: Kuboard
photos: ["https://tc.chaizz.com/5a5bc3060e3d11ed90590242ac140002.png"]
---

参考链接

> [安装 Kuboard v3 - 内建用户库 | Kuboard](https://www.kuboard.cn/install/v3/install-built-in.html)

> Kuboard 是一款专为 Kubernetes 设计的免费管理界面，兼容 Kubernetes 版本 1.13 及以上。Kuboard 每周发布一个 beta 版本，最长每月发布一个正式版本，经过两年的不断迭代和优化，已经具备多集群管理、权限管理、监控套件、日志套件等丰富的功能，并且有 1000+ 的企业将 Kuboard 应用于其生产环境。

一、前期准备

- 搭建完成Kubernetes 集群。 

- Docker 安装完毕

二、安装Kuboard

```shell
sudo docker run -d \
  --restart=unless-stopped \
  --name=kuboard \
  -p 80:80/tcp \
  -p 10081:10081/tcp \
  -e KUBOARD_ENDPOINT="http://192.158.59.130:80" \
  -e KUBOARD_AGENT_SERVER_TCP_PORT="10081" \
  -v /root/kuboard-data:/data \
  eipwork/kuboard:v3
```

三、访问 Kuboard

在浏览器输入 `http://your-host-ip:80` 即可访问 Kuboard v3.x 的界面，登录方式：

- 用户名： `admin`
- 密 码： `Kuboard123`

四、问题