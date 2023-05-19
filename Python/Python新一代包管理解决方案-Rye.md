---
title: Python新一代包管理解决方案-Rye
author: chaizz
date: 2023-5-19
tags: Rye
categories: Python
photos: ["https://origin.chaizz.com/favicon.svg"]
---

​    

<!--more-->

# Python新一代包管理解决方案-Rye

最近Flask之父开源了一款使用Rust开发的Python包管理器：Rye，在[官网](https://rye-up.com/)上对其的解释为一个实验性的Python包管理解决方案。作者说灵感来自于 Rust 的 `rustup` 和 `cargo`。

目前 Rye 还是处于试验阶段，并不能应用到生产中。

Flask 之父之所推出此包管理器，是因为他觉得Python在各个平台并没有一个统一的二进制发行版，没有开发依赖这个明确的概念和没有工作区的规范。

作者希望Python能够有想Rust的cargo一样有完整的触发测试运行、启动构建过程、开发文档构建工具、linters 以及诸如工作区管理、依赖关系管理和包发布之类的工具。

下文是一些简单实用总结 （本文所有操作在windows平台下）。



## 1 安装

windows 下载安装直接下载官网提供的安装包即可（在安装的时候建议开启科学上网）。安装默认将所有的内容安装到 `C:\Users\username\.rye`中。

如果想更改它的位置，可以提前将 `RYE_HOME`添加到环境变量中，安装执行文件会自动读取此环境变量指定的位置。

![image-20230519151233261](https://origin.chaizz.com/tc/image-20230519151233261.png)

例如我的环境变量配置为：`RYE_HOME:D:\Rye`，

### 1.1 配置环境变量

在安装过程中会提示安装位置， 将 `D:\Rye\shims` 如果没有更改安装位置则是`%USERPROFILE%\.rye\shims` 添加到系统环境变量Path中去。将其放在最顶端。 然后重启系统，使其生效。

### 1.2 更新

```
rye self update
```

### 1.3 卸载

```
rye self uninstall
```

### 1.4 禁用自动安装

```powershell
set RYE_NO_AUTO_INSTALL=1
```



## 2 基础配置

初始化一个项目， rye 会自动创建一个my_project文件夹。

```powershell
rye inti my_project
```

当我们使用 Rye 创建一个新项目时， 他会自动帮我们安装一系列配置文件。

```she
.
├── .git
├── .gitignore
├── .python-version
├── README.md
├── pyproject.toml
└── src
    └── my_project
        └── __init__.py
```



其中的 `pyproject.toml` 是 Rye 针对当前项目创建的一个配置文件，`.python-version` 是当前项目的python所使用的的版本。

- [project]:包含了一些项目的基础信息

  - 项目名称
  - 项目介绍
  - 版本号
  - 作者
  - 依赖
  - Python最低版本
  - readme。
  - ...

- [tool.rye]: 一些开发依赖

  - dev-dependencies：表示针对开发环境的一些依赖， 通过 `rye add xx --dev` 添加。

  - excluded-dependencies：包含从未安装的依赖项，即使它们作为间接依赖项被引入也是如此，自动添加的， 可以通过 `rye add xxx --excluded` 添加

  - managed：仅是一个提示，代表由rye管理。

    

- [tool.rye.scripts]: 可注册公开的脚本

  - 每个key都是一个脚本，使用`rye run  具体的key`，例如下文的`rye run dev`



示例：

```toml
[project]
name = "my-project"
version = "0.1.0"
description = "Add a short description here"
authors = [
    { name = "username", email = "username@163.com" }
]
dependencies = ["flask~=2.3.2", "numpy~=1.24.3"]
readme = "README.md"
requires-python = ">= 3.8"

[build-system]
requires = ["hatchling"]
build-backend = "hatchling.build"

[tool.rye]
managed = true
dev-dependencies = ["requests~=2.30.0"]
excluded-dependencies = ["redis"]


[tool.rye.scripts]
# These three options are equivalent:
dev = "python ./src/my_project/hello.py"
```



## 3 使用

### 3.1 初始化配置

使用`rue sync`命令 rye 将创建虚拟环境 .venv，同时也会创建依赖相关的文件。（类似前端的包管理工具的 npm init ?）

```powershell
rue sync
```

如果想更改python的版本， 可以使用 `rye pin 3.10`， 后面跟具体的python版本号。

### 3.2 安装依赖  

当使用`rye add  xx` 之后， 虚拟环境并不会实际安装依赖包， 需要再次执行`ryr sync`才会真正的安装。

```powershell
rye add flask
```

### 3.3 卸载依赖

```powd
rye remove flask
```

### 3.4 启动项目

运行项目有两种方式：

一是在pyproject.toml 中 配置 [tool.rye.scripts] 启动脚本，可以直接使用`rye run 命令` 来运行项目。

```
rye run xxx  // 此处xxx 是在 tool.rye.scripts 中配置的 key
```

二是激活虚拟环境，在执行`ryr sync`之后会生成 .venv 虚拟环境目录， 可以在虚拟环境下操作项目。和 virtualenv 的操作方式一致。

```powershell
# 启动虚拟环境
.venv/scripts/activate

# 退出虚拟环境
deactivate 
```

## 4 结语

随着越来越多的应用使用Rust构建中， 似乎Python的项目管理也好、依赖管理也好，也可以像前端的包管理工具一样有更多的可能性。

