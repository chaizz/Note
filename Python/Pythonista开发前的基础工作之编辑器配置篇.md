---
title: Pythonista开发前的基础工作之编辑器配置篇
author: chaizz
date: 2023-06-14
tags:
  - Pycharm
categories:
  - Pycharm
photos: ["https://origin.chaizz.com/PyCharm.png"]
cover: "https://origin.chaizz.com/PyCharm.png"
---

​    

<!--more-->

# Pythonista开发前的基础工作之编辑器配置篇

## 1 编辑器基础配置

### 1.1 外观

- 字体：Fira Code Medium  大小：16

### 1.2 编辑器

- 字体：Fira Code  大小：18

### 1.3 Atom Material Icons Setting

​	![image-20230731181034618](https://origin.chaizz.com/tc/image-20230731181034618.png)



![image-20230731181125760](https://origin.chaizz.com/tc/image-20230731181125760.png)



## 2 快捷键

- 禁用双击shift唤醒全局搜索框 [shift-shift] 

  设置 -> 高级设置 -> 用户界面

  ![](https://origin.chaizz.com/tc/image-20230614153241084.png)



## 3 插件

- .ignore  忽略文件

- Translation 翻译插件， 需要自己设置翻译SDK的key 
- Atom Material Icons   一个好看的文件夹主题
- GitToolBox    git 相关插件， 显示历史修改人等
- One Dark Theme  黑色主题
- Tabnine AI Code Completion AI开发助手
- sourcery  基于AI的编码助手
- LeetCode Editor leetcode 插件



## 4 代码模板

-  设置 -> 编辑器 -> 文件和代码模板 -> Python Script 

  ```
  """
  -------------------------------------------------
      File Name:   ${NAME}.py
      Description: 
          
      Author:      chaizz
      Date:        ${DATE} ${TIME}
  -------------------------------------------------
      Change Activity:
            ${DATE} ${TIME}
  -------------------------------------------------
  """
  ```

  
