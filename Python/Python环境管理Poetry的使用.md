---
title: Python环境管理Poetry的使用
author: chaizz
date: 2023-03-23
tags: Poetry
categories: Poetry
photos: ["https://tc.chaizz.com/8aa11db4ca1611ed8b330242ac190002.png"]
---

​    

<!--more-->



>Poetry 是 Python 中依赖管理和打包的工具。他可以管理项目中的第三包的依赖（安装/更新）。T同时也提供了一个锁定文件以确保可重复安装，并且可以构建项目以供分发。

poetry的Python版本要求为 3.7+，且是多平台的。



安装Poetry (在windows平台下)

```powershell
(Invoke-WebRequest -Uri https://install.python-poetry.org -UseBasicParsing).Content | python -
```

根据官网安装的提示：如果是在Microsoft Store上安装的python，需要将上面的py替换为python，但是我直接安装，提示 `py : 无法将“py”项识别为 cmdlet、函数、脚本文件或可运行程序的名称`。必须要把py替换为python。

使用 `poetry --version` 检查版本， 如果提示版本号则安装成功。

更新Poetry (在windows平台下)

```powershell
poetry self update
```

卸载Poetry (在windows平台下)

```powershell
(Invoke-WebRequest -Uri https://install.python-poetry.org -UseBasicParsing).Content | python - --uninstall
```



poetry 安装后默认的缓存路径个虚拟环境路径在:

`C:\Users\<your name>\AppData\Local\pypoetry\Cache` 为了不占用C盘空间，修改默认的虚拟环境的目录。

```powershell
# 此处直接修改 cache-dir 就会修改 cache-dir 和 virtualenvs.path 的路径
poetry config cache-dir D:\\poetry_enev 
```



通过 `poetry config --list` 查看已经修改成功

![](https://tc.chaizz.com/33b9dcacca2011ed8b330242ac190002.png)

> 取消设置：
>
> ```powershell
> poetry config cache-dir --unset
> ```





使用poetry创建一个新项目

1. 直接使用poetry 创建项目 

   ```powershell
   poetry new project_name 
   ```

   自动创建一个project_name的文件件，项目名称也叫做project_name。

2. 添加python依赖

   ```powershell
   # 在pyproject.toml 同目录下
   poetry add numpy 
   ```

   安装的包和版本都会在  pyproject.toml 中

   ```toml
   [tool.poetry.dependencies]
   python = "^3.9"
   numpy = "^1.24.2"
   ```

3. 运行项目或者文件

   ```powershell
   poetry run python run.py 
   ```

   

在新现有项目中使用poetry

1. 在某个目录下 使用poetry init

   ```powershell
   poetry init
   ```

   使用这种交互式的方式创建项目，根据选项设置完成之后，在对应的目录下同样会生成 pyproject.toml 文件。

2. 添加依赖和运行项目和与 poetry new 一致。



拿到一个使用poetry管理依赖的项目

1. 在pyproject.toml同目录下

   ```powershell
   poetry install
   ```

2. 如果要将依赖跟新到最新版本

   ```powershell
   poetry update
   ```

   

> Tips: 使用poetry时会生成 poetry.lock，此文件推荐上传到git版本控制中，

 



手动管理依赖

1. 在 pyproject.toml文件同目录下，进入虚拟环境

   ```powershell
   poetry shell
   ```

   使用此命令，进入当前的项目虚拟环境，接下来就像平常使用python一样了。

2. 退出

   ```powershell
   deactivate 
   # 或者直接退出 这个是shell
   ```



管理环境

