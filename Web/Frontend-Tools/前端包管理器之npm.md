---
title: 前端包管理器之npm
author: chaizz
date: 2022-8-18
tags: npm
photo: ["https://origin.chaizz.com/npm_run.png"]
---

​          

<!--more-->

# 前端包管理器之npm

npm是一个软件注册中心，有大量的开发者和组织将软件上传到npm。npm是由三部分构成， website、cli、register。 我们最常用的就是npm cli。

## 1 安装npm
要使用npm, 需要安装node.js和npm软件，node.js 是一个异步事件驱动的 JavaScript 运行时。

node.js 就像其他的语言一样，有很多的版本， npm官网推荐使用`nvm`--一个node版本管理器，为了解决nodejs各种版本不兼容的问题，类似Python的conda。

nvm: 一个node版本管理器， 提供不同版本的node和npm安装。

node.js: 一个JavaScript运行时。

npm: 一个前端包管理器。

## 2 安装nvm 
在windwos上安装 nvm [Github地址](https://github.com/coreybutler/nvm-windows)，[下载地址](https://github.com/coreybutler/nvm-windows/releases)。 直接下载 .exe 可执行文件进行安装。

常用命令：

```powershell
nvm version # 显示nvm的版本

nvm list # 列出当前系统安装的node.js 版本列表，使用【*】开头的代表，正在使用的版本。

nvm list available # 列出可供下载的nodejs版本

nvm url 

nvm install <版本号>  [--insecure]   # 安装指定版本的nodejs， 加上--insecure绕过远程验证。

nvm root <path>: # 设置nvm存放不同版本node.js的目录。如果<path>未设置，将显示当前根目录。

nvm node_mirror <node_mirror_url> # 设置node国内镜像：https://npmmirror.com/mirrors/node/

nvm npm_mirror <npm_mirror_url> # 设置npm国内镜像：https://npmmirror.com/mirrors/npm/

# 以上三个配置也可以到nvm的安装路径下的settings.txt设置
```

使用nvm install nodejs版本号。 即可安装和nodejs匹配的npm。



## 3 npm的常用命令

### 3.1 查询npm的版本

```powershell
npm -v 
```

### 3.2 初始化或更新一个包

```powershell
npm init 

# 生成它而不让它问任何问题
npm init -y 
```

### 3.3 安装一个包

```powershell
npm installl package

# 别名 i，add, in, ins, inst, insta, instal, isnt, isnta, isntal, isntall
# npm install package -g   全局安装

# npm i package@latest 指定最新版本， 或者版本号
```

### 3.4 全新安装项目 （安装一个干净的项目）

```powershell
npm ci
# 别名: clean-install, ic, install-clean, isntall-clean

# 此命令与 npm install 类似，不同之处在于它旨在用于自动化环境，例如测试平台、持续集成和部署——或任何您希望确保对依赖项进行全新安装的情况。

#使用 npm install 和 npm ci 的主要区别是：
# 1.该项目必须具有现有的 package-lock.json 或 npm-shrinkwrap.json。
# 2. 如果包锁中的依赖项与 package.json 中的依赖项不匹配，npm ci 将退出并出错，而不是更新包锁。
# 3. npm ci 一次只能安装整个项目： 不能使用此命令添加单个依赖项。
# 4. 如果 node_modules 已经存在，它将在 npm ci 开始安装之前自动删除。
# 5. 它永远不会写入 package.json 或任何包锁： 安装基本上被冻结了。
```

### 3.5 管理 npm 配置文件

```powershell
npm config set <key>=<value> [<key>=<value> ...]
npm config get [<key> [<key> ...]]
npm config delete <key> [<key> ...]
npm config list [--json]
npm config edit
npm config fix
# 别名: c
```

### 3.6 列出安装的包

```powershell
npm ls 

# npm ls --all 列出所有包的依赖
```

### 3.7 启动一个包

```powershell
npm start 

# 这将运行在包的 "scripts" 对象的 "start" 属性中指定的预定义命令。如果 "scripts" 对象没有定义 "start" 属性，npm 将运行 node server.js。
```

### 3.8 停止一个包

```powershell
npm stop 

# 这将运行在包的 "scripts" 对象的 "stop" 属性中指定的预定义命令。与 npm start 不同，如果未定义 "stop" 属性，则不会运行默认脚本。
```

### 3.9 测试一个包

```powershell
npm test 
# 这将运行在包的 "scripts" 对象的 "test" 属性中指定的预定义命令。
```

### 3.10 卸载一个包

```powershell
npm uninstall package
# 别名： unlink, remove, rm, r, un

# 这将卸载一个包，完全删除代表它安装的所有 npm。
# 它还会从 package.json 中的 dependencies、devDependencies、optionalDependencies 和 peerDependencies 对象中删除包。

# 使用 --no-save  不会从package.json、npm-shrinkwrap.json 或 package-lock.json 文件中删除。
```

### 3.11 更新包

```powershell
npm update package

# 别名: up, upgrade, udpate

# 此命令会将列出的所有包更新到最新版本（由 tag 配置 指定），同时尊重包及其依赖项的 semver 约束（如果它们也需要相同的包）。

# 它还将安装缺少的包。

# 如果指定了 -g 标志，此命令将更新全局安装的包。

# 如果未指定包名称，则将更新指定位置（全局或本地）中的所有包。

# 请注意，默认情况下，npm update 不会更新项目 package.json 中直接依赖项的 semver 值，如果你还想更新 package.json 中的值，你可以运行： npm update --save
```



详细的命令来自 [npm中文网](https://npm.nodejs.cn/)。
