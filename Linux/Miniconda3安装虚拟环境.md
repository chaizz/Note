---
title: Miniconda3安装虚拟环境
author: chaizz
date: 2021-05-09 16:04:46
tags: Miniconda
categories: Miniconda
photos: ["https://tc.chaizz.com/Snipaste_2021-05-09_16-08-33.png"]
---
​                   

<!--more-->

创建虚拟环境

```shell
conda create -n [name] python=版本 -y 
```

```shell
# 激活环境
conda activate name
# 关闭环境
deactivate 
```

在对应的环境中安装包 

```shell
pip install xxx
conda install xxx
```

运行jupyter notebook 遇到错误 

- 提示无法导入某个包

问题原因：

jupyter notebook 使用的还是原来的python 包，无法使用新的虚拟环境的包。

点击 jupyter notebook 的new 查看是否能够找到新创建的环境。

![](https://tc.chaizz.com/Snipaste_2021-05-08_18-59-42.png)

解决办法：

```shell
# ipykernel在虚拟环境下安装 ipykernel。
conda install -n 环境名称 ipykernel
# 将创建的新的虚拟环境，添加到核心。
python -m ipykernel install --user --name 环境名称 --display-name "环境名称"
# 接下来重新打开 jupyter notebook即可。
```

- 运行代码时提示：内核似乎已经死了 . 它会自动重启 。

问题原因：

再学习DIVE INTO DEEP LEARNING 这本书中的代码时，运行某一段输出图表的内容的时候，提示内核似乎已经死了 . 它会自动重启 。 无法输出图表。

在Pycharm 上提示的错误为 ：

![](https://tc.chaizz.com/Snipaste_2021-05-08_19-10-19.png)

解决办法：

在代码前导入以下代码即可

```python
import os
os.environ['KMP_DUPLICATE_LIB_OK'] = 'TRUE'
```



> [jupyter notebook无法使用conda环境的模块](https://blog.csdn.net/qq_43382616/article/details/108642590)

> [jupyter notebook内核挂掉了，需要重启](https://blog.csdn.net/scar2016/article/details/115710308)

