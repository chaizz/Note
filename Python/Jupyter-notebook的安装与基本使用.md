---
title: Jupyter-notebook的安装与基本使用
author: chaizz
date: 2021-03-13 11:41:22
tags: Jupyter-notebook
categories: Jupyter-notebook
photos: ["https://origin.chaizz.com/ddec75de4ad011ec9d7c5254006b8f1d.jpeg"]
---

​              

<!--more-->

> #### The Jupyter Notebook is an open-source web application that allows you to create and share documents that contain live code, equations, visualizations and narrative text. Uses include: data cleaning and transformation, numerical simulation, statistical modeling, data visualization, machine learning, and much more.

以上为Jupyter notebook 的官方解释。

> Jupyter Notebook是一个开源Web应用程序，可让您创建和共享包含实时代码，方程式，可视化效果和叙述文本的文档。 用途包括：数据清理和转换，数值模拟，统计建模，数据可视化，机器学习等.



## 一、windows下安装Jupyter notebook

Jupyter notebook 是基于Python 的首先要安装Python环境，或者直接安装Anaconda（直接包含Python和Jupyter notebook）。以上环境省略自行安装。

### 在Python环境下直接安装。（最好是新版本Python）

```shell
# 在windows shell 终端输入：
pip install jupyter
```

等待命令安装完毕，如果比较慢的话，建议更换Python的源为清华或者淘宝网易的源下载安装速度会快很多。

### 启动Jupyter notebook 

系统会自动启动浏览器，打开本地的8888端口。（端口没有被占用的情况下。如果被占用端口号会自动加一，启动多个Jupyter notebook应用，端口号也会自动向后加一来启动多个应用。）

```shell
# 在windows shell 终端输入：
jupyter notebook 
```

如果在使用过程中关闭了当前终端，应用也会随之无法连接，无法使用Jupyter notebook应用。



如果要以不同的端口号启动Jupyter notebook应用，使用一下的方式启动。

```shell
# 在windows shell 终端输入：
jupyter notebook --port 8899
```

## 二、Jupyter notebook 界面

Jupyter notebook 的默认存储文件的地址为当前用户的文件路径。每个人里面的文件都不太一样。

### 更改默认的存储路径。

Jupyter notebook  的默认的配置文件在当前用户下的一个隐藏文件（以.jupyter开头的文件）文件下名为jupyter_notebook_config.py 即为jupyter notebook 的配置文件。

打开文件搜索找到 `c.NotebookApp.notebook_dir` 开头的配置，即是存储文件路径的配置。只需要将你想要修改的路径添加并取消注释即可。

```
c.NotebookApp.notebook_dir = 'C:/Users/xxx/Desktop/Project/Jupyter Notebook'
```

重启启动 Jupyter notebook。此时页面文件目录是空的。

## 三、使用 Jupyter notebook

新建一个页面。 点击右上角的 new 按钮可以新建一个工程文件或者文本文件或者文件夹。

![](https://origin.chaizz.com/717fe24c4ade11ec9d7c5254006b8f1d.png)

例如 新建一个python3文件。新页面样式如图所示。

![](https://origin.chaizz.com/7fcf69304ade11ec9d7c5254006b8f1d.png)

可在单元格（cell）中输入代码并执行。

## 四、Jupyter notebook 快捷键

A：在当前单元格的上方添加一行单元格

B：在当前单元格的下方添加一行单元格

M：选中单元格将单元格代码格式改为Markdown格式

Y：选中单元格将单元格代码格式改为代码格式

X：剪切当前单元格

V：在当前单元格下方粘贴复制或者剪切的单元格

Enter：在当前单元格换行

Shift+Enter：代码模式下执行当前单元格代码

shift+V：在当前单元格上方粘贴复制或者剪切的单元格

D D ：连续两个D 删除当前单元格

Z：撤销删除单元格

还有很多快捷键按H都可以找到。

## 五、Jupyter notebook 插件

在安装插件之前首先需要安装**jupyter_contrib_nbextensions**。

```shell
# 在windows shell 终端输入：
pip install jupyter_contrib_nbextensions

# 启用 jupyter_contrib_nbextensions
jupyter contrib nbextension install --user
```

此时 Jupyter notebook 界面就会出现一个新的菜单栏。

![](https://origin.chaizz.com/8a881e4e4ade11ec9d7c5254006b8f1d.png)

选中此按钮进入配置插件的页面。

取消勾选此按钮。否则无法选择插件。

![](https://origin.chaizz.com/941816a84ade11ec9d7c5254006b8f1d.png)

推荐插件：

- **Notify** ：能在任务处理完后及时向你发送通知
- **Hinterland** ：自动补全代码
- **Codefolding**  ：折叠代码
- **Table of Contents(2)**  ：自动生成导航目录
- **Autopep8** ：自动格式化代码。（需要安装python库：autopep8）
- **ExecuteTime** ：显示单元格的运行时间和耗时 
- **Split Cells Notebook** ：类似于多窗口编辑，将单行排列的单元格放置为多行排列。
- **Collapsible Headings** ：折叠单元格