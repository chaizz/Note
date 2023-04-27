---
title: 前端包管理器之yarn
author: chaizz
date: 2022-8-18
tags: yarn
photo: ["https://origin.chaizz.com/a1da7edce4bd11ed9a340242ac190002.png"]
---

​          

<!--more-->

安装yarn 

```powershell
npm install -g yarn

# 或者 pnpm add -g yarn
```



### 初始化一个新项目

```bash
yarn init
```

### 安装所有依赖项

```bash
yarn
yarn install
```

### 添加依赖项

```bash
yarn add [package]
yarn add [package]@[version]
yarn add [package]@[tag]
```

### 将依赖项添加到不同的依赖类别中

```bash
yarn add [package] --dev  # dev dependencies
yarn add [package] --peer # peer dependencies
```

### 更新依赖项

```bash
yarn up [package]
yarn up [package]@[version]
yarn up [package]@[tag]
```

### 删除依赖项

```bash
yarn remove [package]
```

### 更新 Yarn 本体

```bash
yarn set version latest
yarn set version from sources
```
