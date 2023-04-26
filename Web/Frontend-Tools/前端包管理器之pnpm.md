---
title: 前端包管理器之pnpm
author: chaizz
date: 2022-8-18
tags: CSS
photo: ["https://origin.chaizz.com/dbf0a4d4e40411ed8b5f0242ac190002.jpg"]
---

​          

<!--more-->

# 前端包管理器之pnpm

[pnpm](https://pnpm.io/zh/motivation) 正如他官网的口号一样：速度快、节省磁盘空间的软件包管理器。

主要解决了npm的一些痛点：

- 如果有多个项目，那么使用npm的情况下每个项目就会有各自的依赖，可能会造成依赖的重复，浪费磁盘空间。
- 不同版本的依赖，存储在同一位置，且不会因为版本的不同而修改保存依赖包的所有文件。

最终的体现结果就是依赖的存储空间变少了，速速也会变快，官方声称构建速度是同类构建工具的将近2倍。



## 1 安装pnpm

建议写卸载现有的npm,node和nvm等其他的包管理器，和node版本管理器。

没有安装nodejs的情况下安装

```powershell
iwr https://get.pnpm.io/install.ps1 -useb | iex
```

已经安装了npm的情况下安装, 直接全局安装。

```powershell
npm install -g pnpm
```

安装后修改如果嫌pnpm的命令长，可以改为短命令：pn

在windows中只需要两步：

```powershell
# 1、使用管理员打开终端
notepad $profile.AllUsersAllHosts

# 2、在 profile.ps1 文件里加入：
set-alias -name pn -value pnpm

# 保存后，打开新的窗口直接使用 pn -v
```



## 2 pnpm的常用命令

大部分都和npm一致, 少数会有区别

### 2.1 node环境管理

```powershell
# 查看有哪些可安装的node版本
pn env list --remote

# 查看本地已经安装的node版本 或者是  pn env ls
pn env list 

# 安装/切换具体的node lts 版本, 或者指定版本号， 或者是 latest最新稳定版本   --global 可以简写为-g 和npm使用 别名差不多。 
pn env use --global lts

# 删除指定版本的node
pn env remove --global 14.0.0
```



### 2.2  设置 pnpm 配置 

Windows路径为：**C:\Users\<UserName>\.npmrc**，可以手动打开进行配置

```powershell
# 列出当前配置   -g 列出全局配置
pn config list

# 修改 pnpm 的源
# 淘宝
pn config set registry https://registry.npmmirror.com

# 腾讯
pn config set registry http://mirrors.cloud.tencent.com/npm/

# 华为
pn config set registry https://repo.huaweicloud.com/repository/npm/
```

### 2.3 安装/更新/卸载包和相关依赖

```powershell
# -g 全局安装  -D 保存到 devDependencies
pn add -g pkg


# 更新包
# 在不带参数的情况下使用时，将更新所有依赖关系
pn update pkg



# 卸载 -g 全局卸载
pn remove -g pkg
# 别名：rm, uninstall, un
```







