---
title: VitePress搭建文档网站
author: chaizz
date: 2022-11-30
tags: VitePress
photo: ["https://tc.chaizz.com/438d4290709311edb4470242ac190002.svg"]
---

​            

<!--more-->

# VitePress搭建文档

> 在VitePress官方文档中指明它是基于Vite构建的一个VuePress的小兄弟，同时也对VuePress存在的几个问题进行一些改进：
>
> -  更快
> -  使用Vue3
> -  更加轻巧



## 一、安装

[以下步骤来自官网](https://vitejs.cn/vitepress/guide/getting-started.html)

**步骤 1:** 创建并进入一个目录

```sh
$ mkdir vitepress-starter && cd vitepress-starter
```

**步骤 2:** 初始化

```sh
$ yarn init
```

**步骤 3:** 本地安装 VitePress

```sh
$ yarn add --dev vitepress
```

**步骤 4:** 创建你第一篇文档

```sh
$ mkdir docs && echo '# Hello VitePress' > docs/index.md
```

**步骤 5:** 在 `package.json`.添加一些`script`

```sh
{
  "scripts": {
    "docs:dev": "vitepress dev docs",
    "docs:build": "vitepress build docs",
    "docs:serve": "vitepress serve docs"
  }
}
```

**步骤 6:** 在本地服务器上启动文档站点

```sh
$ yarn docs:dev
```

VitePress 会在 `http://localhost:5173 `启动一个热重载的开发服务器。



## 二、设置基本信息

进入项目根目录，在根目录中有一个 `docs` 文件夹，进入并创建 `.vitepress` 文件夹。在 `.vitepress` 文件夹中创建VitePress的配置文件 `config.js`。

目录结构为：

```
|--docs
|	|--.vitepress
|	|	|--config.js
|	|--index.md
```



在配置文件中为网站设置相关信息：

```js
module.exports = {
    title: '网站标题',
    description: '网站介绍',
    themeConfig: {
        logo: "/logo.svg",
    }
}
```

其中 `themeConfig.logo `配置可以配置网站首页的图标, 需要指定图标文件在公共文件夹下。即在 `docs` 文件夹下新建 `public` 文件，然后将 `logo.svg` 存放在此文件夹下。一些公共图片等也都可以放在此文件夹下。

![](https://tc.chaizz.com/d963f51c709711edb4470242ac190002.png)

目录结构为：

```js
|--docs
|	|--.vitepress
|	|	|--config.js
|	|--public
|	|	|--logo.svg
|	|--index.md
```



## 三、设置导航栏

在 `config.js` 中配置 `themeConfig.nav` 设置导航栏。

```js
module.exports = {
    title: '网站标题',
    description: '网站介绍',
    themeConfig: {
        logo: "/logo.svg",
        nav: [
            {
                text: "Guide",
                items: [
                    {text: 'guide 1', link: '/guide/guide-1'},
                    {text: 'guide 2', link: '/guide/guide-2'},
                    {text: 'guide 3', link: '/guide/guide-3'}
                ]
            }
        ]
}
```



`nav` 是一个数组，每个元素代表一个导航栏标签，标签有两个属性 `text` 和 `link` 。

`text` ：标签名`link` ：跳转的url。也可以使用下拉导航栏，例如上面中的示例。此时 `link` 元素改为 `items`，在 `items` 中是每个标签的属性。导航栏相对应的跳转url, 是在 `docs` 文件夹下创建同名的文件夹。

**非下拉导航栏**格式为：

```
nav: [
    {
        text: "Guide",
        link: "/guide/"
    }
],
```

目录结构为：

```js
|--docs
|	|--guide
|	|	|--index.md
|	|--.vitepress
|	|	|--config.js
|	|--public
|	|	|--logo.svg
|	|--index.md
```

**下拉导航栏**格式为：

```js
nav: [
    {
        text: "Guide",
        items: [
            {text: 'guide 1', link: '/guide/guide-1'},
            {text: 'guide 2', link: '/guide/guide-2'},
            {text: 'guide 3', link: '/guide/guide-3'}
        ]
    }
],
```

目录结构为：

```js
|--docs
|	|--guide
|	|	|--guide-1.md
|	|	|--guide-2.md
|	|	|--guide-3.md
|	|--.vitepress
|	|	|--config.js
|	|--public
|	|	|--logo.svg
|	|--index.md
```



## 四、设置侧边栏

在 `config.js` 中配置 `themeConfig.sidebar` 设置侧边栏。

```js
module.exports = {
    title: '网站标题',
    description: '网站介绍',
    themeConfig: {
        logo: "/logo.svg",
        nav: [
            {
                text: "Guide",
                items: [
                    {text: 'guide 1', link: '/guide/guide-1'},
                    {text: 'guide 2', link: '/guide/guide-2'},
                    {text: 'guide 3', link: '/guide/guide-3'}
                ]
            }
        ],
        sidebar: [
            {
                text: "web",
                items: [
                    {text: "js", link: "/JS/",},
                    {text: "css", link: "/css/"},
                ],
            }
        ],

    }
}
```



`sidebar` 和 `nav` 设置方式一致，二级菜单使用 `items` 设置。

目录结构为：

```js
|--docs
|	|--guide
|	|	|--guide-1.md
|	|	|--guide-2.md
|	|	|--guide-3.md
|	|--.vitepress
|	|	|--config.js
|	|--public
|	|	|--logo.svg
|	|--css
|	|	|--index.md
|	|--js
|	|	|--index.md
|	|--index.md
```

