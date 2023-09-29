---
title: Pythonista开发前的基础工作之浏览器篇
author: chaizz
date: 2023-09-29
tags:
  - 开发环境配置
categories:
  - 开发环境配置
  - 浏览器配置
photos: ["https://origin.chaizz.com/tc/DALL·E 2023-04-06 17.04.16 - A cartoon version of a programmer.png"]
cover: "https://origin.chaizz.com/tc/DALL·E 2023-04-06 17.04.16 - A cartoon version of a programmer.png"
---

​    

<!--more-->

# Pythonista开发前的基础工作之浏览器篇

## 1 浏览器的选择

浏览器这个东西因人而异，每个人的使用习惯不一样，根据自己的习惯选择就好。

目前的主流浏览器有：[Chrome](https://www.google.com/chrome/)、[Edge](https://www.microsoft.com/edge)、[Safari](https://www.apple.com/tw/safari/)、[Firefox](https://www.mozilla.org/firefox/new/)。

之前还听说过一个浏览器叫做[ARC](https://arc.net/)，使用习惯好像上面那些传统的浏览器不一致，比较新颖。不过目前好像不支持windows，也没用过，使用过的小伙伴可以说一下感受。

> 我的选择：**Chrome**。

之前参考过一篇[文章](https://tw93.fun/2023-08-20/edge.html)，然后倒腾了下Edge dev版本, 使用的侧边栏的形式，也是使用了大概两周，但是在一些方面感觉还是不如**Chrome**用的顺手，就又换了回去。

## 2 浏览器设置

首先Google账号必须要设置的，不使用账号登录的话，同步功能无法很好的使用。

浏览器的一些具体配置，首先打开浏览器的设置界面：  

第一栏`您与Google`-> `同步功能和 Google 服务`的`允许登录`和`改进搜索建议`是开启的，其他选项都是关闭的。

![](https://origin.chaizz.com/tc/image-20230929232249560.png)

第三栏`隐私与安全`->`隐私保护指南`里面`历史同步记录`是开的，保护级别为`标准保护`，第三方 Cookie 偏好设置为`在无痕模式下阻止第三方 Cookie`。

`隐私与安全`->`广告隐私权设置`里面的所有能选择的按钮，我全是关的。最近Google出了这个推送广告服务，看来要和百度如出一辙了。

第四栏`性能`->`内存节省程序` 设置为开。

其他的就没有特别的设置了。

## 3 扩展程序

扩展程序我安装的比较多，都是将一些常用的打开， 不常用的关闭， 等到需要用的时候，再打开使用，也能节省一点内存。

常用的一些扩展

- **AdGuard 广告拦截器** - 拦截广告，感觉效果很好 - [地址](https://chrome.google.com/webstore/detail/adguard-adblocker/bgnkhhnnamicmpeenaelnjfhikgbkllg)
- **IDM Integration Module** - 下载器，无敌好用 - [地址](https://chrome.google.com/webstore/detail/idm-integration-module/ngpampappnmepgilojfohadhhmbhlaek)
- **PureTwitte**r - 用来绿化X的，屏蔽一些黄推 - [地址](https://chrome.google.com/webstore/detail/puretwitter/nflidllhiamnebgbgoemadhhfdpbbpbi)
- **Vue.js devtools** - vuedebug工具 - [地址](https://chrome.google.com/webstore/detail/vuejs-devtools/nhdogjmejiglipccpnnnanhbledajbpd)
- **沉浸式翻译** - 翻译工具 - [地址](https://chrome.google.com/webstore/detail/immersive-translate-web-p/bpoadfkcbjbfhfodiogcnhhhpibjhbnh)
- **沙拉查词-聚合词典划词翻译** - 划词翻译 - [地址](https://chrome.google.com/webstore/detail/%E6%B2%99%E6%8B%89%E6%9F%A5%E8%AF%8D-%E8%81%9A%E5%90%88%E8%AF%8D%E5%85%B8%E5%88%92%E8%AF%8D%E7%BF%BB%E8%AF%91/cdonnmffkdaoajfknoeeecmchibpmkmg)
- **稀土掘金** - 一般我会用来当做浏览器首页 - [地址](https://chrome.google.com/webstore/detail/%E7%A8%80%E5%9C%9F%E6%8E%98%E9%87%91/lecdifefmmfjnjjinhaennhdlmcaeeeb)
- **类似的网站** - 查询类型的网站 - [地址](https://chrome.google.com/webstore/detail/similar-sites-discover-re/necpbmbhhdiplmfhmjicabdeighkndkn)
- **篡改猴** - 就是油猴，后续也会介绍一些常用的脚本 - [地址](https://chrome.google.com/webstore/detail/tampermonkey/dhdgffkkebhmkfjojejmpbldmpobfkfo) 
- **购物党自动比价工具** - 购物比价，不过现在都不在网页上购物了， 有时候会打开看看价格走势 - [地址](https://chrome.google.com/webstore/detail/%E8%B4%AD%E7%89%A9%E5%85%9A%E8%87%AA%E5%8A%A8%E6%AF%94%E4%BB%B7%E5%B7%A5%E5%85%B7/jgphnjokjhjlcnnajmfjlacjnjkhleah)

不常用的扩展：

- **AI沉浸翻译和YouTube/Netflix双字幕** - 这个东西可以在看外文视频网站时打开实时翻译，是一个很不错的学英语的工具 - [地址](https://chrome.google.com/webstore/detail/ai-translator-and-youtube/mjdbhokoopacimoekfgkcoogikbfgngb)
- **Axure RP Extension for Chrome** - 查看原型时使用 - [地址](https://chrome.google.com/webstore/detail/axure-rp-extension-for-ch/dogkpdfcklifaemcdfbildhcofnopogp)
- **Dark Reader** - 使没有夜间主题的网站手动变为夜间主题 - [地址](https://chrome.google.com/webstore/detail/dark-reader/eimadpbcbfnmbkopoojfekhnkhdbieeh)
- **GitZip for github** - 可以单独下载github仓库的文件夹 - [地址](https://chrome.google.com/webstore/detail/gitzip-for-github/ffabmkklhbepgcgfonabamgnfafbdlkn)
- **JSON格式化工具** - 美化web端的json - [地址](https://chrome.google.com/webstore/detail/json%E6%A0%BC%E5%BC%8F%E5%8C%96%E5%B7%A5%E5%85%B7/hldkiepdcdeibnadnpiekpbdgbgbalob)
- **Minimal Theme for Twitter** - x的美化主题 - [地址](https://chrome.google.com/webstore/detail/minimal-theme-for-twitter/pobhoodpcipjmedfenaigbeloiidbflp)
- **Nimbus 截幕 & 屏幕录像机** - 使用这个工具一般用来网页录屏，截图都用Snipaste - [地址](https://chrome.google.com/webstore/detail/nimbus-screenshot-screen/bpconcjcammlapcogcnnelfmaeghhagj)
- **Simple Allow Copy** - 解除网站复制的，一些油猴脚本也可以实现 - [地址](https://chrome.google.com/webstore/detail/simple-allow-copy/aefehdhdciieocakfobpaaolhipkcpgc)
- **Volume Master** - 可以增大网页播放器音量 - [地址](https://chrome.google.com/webstore/detail/volume-master/jghecgabfgfdldnmbfkhmffcabddioke)
- **XPath Helper** - 爬虫必备 - [地址](https://chrome.google.com/webstore/detail/xpath-helper/hgimnogjllphhhkhlmebbmlgjoejdpjl)
- **捕捉网页截图** - 网页截图工具，保存为PDF - [地址](https://chrome.google.com/webstore/detail/take-webpage-screenshots/mcbpblocgmgfnpjjppndjkmgjaogfceg)
- **猫抓** - 网页媒体休嗅探工具 - [地址](https://chrome.google.com/webstore/detail/%E7%8C%AB%E6%8A%93/jfedfbgedapdagkghmgibemcoggfppbb)

## 4 油猴脚本

- **anti-redirect** - 去除重定向 - [地址](https://greasyfork.org/zh-CN/scripts/11915-anti-redirect)
- **HTML5视频播放器增强脚本** - 很强大的H5播放器，平时看视频可以超高倍速 - [地址](https://greasyfork.org/zh-CN/scripts/381682-html5%E8%A7%86%E9%A2%91%E6%92%AD%E6%94%BE%E5%99%A8%E5%A2%9E%E5%BC%BA%E8%84%9A%E6%9C%AC)
- **Twitter Block Porn** - 同样是屏蔽黄推的 -[地址](https://greasyfork.org/zh-CN/scripts/470359-twitter-block-porn)
- **V2EX Polish** - 美化V2ex - [地址](https://greasyfork.org/zh-CN/scripts/459848-v2ex-polish-%E4%BD%93%E9%AA%8C%E6%9B%B4%E7%8E%B0%E4%BB%A3%E5%8C%96%E7%9A%84-v2ex)
- **百度网盘千千下载助手** - 百度网盘下载助手结合IDM可直接满速下载，**需要关注公众号 **- [地址](https://greasyfork.org/zh-CN/scripts/463171-%E7%99%BE%E5%BA%A6%E7%BD%91%E7%9B%98%E5%8D%83%E5%8D%83%E4%B8%8B%E8%BD%BD%E5%8A%A9%E6%89%8B)
- **知乎修改器** - 美化知乎 - [地址](https://greasyfork.org/zh-CN/scripts/423404-%E7%9F%A5%E4%B9%8E%E4%BF%AE%E6%94%B9%E5%99%A8-%E6%8C%81%E7%BB%AD%E6%9B%B4%E6%96%B0-%E5%8A%AA%E5%8A%9B%E5%AE%9E%E7%8E%B0%E5%8A%9F%E8%83%BD%E6%9C%80%E5%85%A8%E7%9A%84%E7%9F%A5%E4%B9%8E%E9%85%8D%E7%BD%AE%E6%8F%92%E4%BB%B6)
- **知乎隐藏标题** - 在浏览知乎时隐藏知乎的标题 - [地址](https://greasyfork.org/zh-CN/scripts/476351-%E7%9F%A5%E4%B9%8E%E9%9A%90%E8%97%8F%E6%A0%87%E9%A2%98)
- **网盘直链下载助手** - 百度网盘下载助手结合IDM可直接满速下载，**需要关注公众号 ** - [地址](https://greasyfork.org/zh-CN/scripts/436446-%E7%BD%91%E7%9B%98%E7%9B%B4%E9%93%BE%E4%B8%8B%E8%BD%BD%E5%8A%A9%E6%89%8B)
- **CSDN广告完全过滤** - 免登陆直接复制等 - [地址](https://greasyfork.org/zh-CN/scripts/378351-%E6%8C%81%E7%BB%AD%E6%9B%B4%E6%96%B0-csdn%E5%B9%BF%E5%91%8A%E5%AE%8C%E5%85%A8%E8%BF%87%E6%BB%A4-%E4%BA%BA%E6%80%A7%E5%8C%96%E8%84%9A%E6%9C%AC%E4%BC%98%E5%8C%96-%E4%B8%8D%E7%94%A8%E5%86%8D%E7%99%BB%E5%BD%95%E4%BA%86-%E8%AE%A9%E4%BD%A0%E4%BD%93%E9%AA%8C%E4%BB%A4%E4%BA%BA%E6%83%8A%E5%96%9C%E7%9A%84%E5%B4%AD%E6%96%B0csdn)



## 5 配置同步

Chrome的设置和扩展在Chrome的同步数据中都可以打开， 进行直接同步。

油猴脚本的同步需要打开脚本管理面板的同步设置：

`点击油猴插件图标` -> `点击管理面板` -> `右上角点击设置按钮` -> `第一栏通用->配置模式->选择初学者或者高级` -> `下拉会发现 同步脚本 栏` -> `开启启用脚本` -> `可选择同步类型，默认浏览器就行` -> `点击现在运行即可同步`

建议在两个电脑之间同步脚本时，先将一个脚本列表清空在进行同步， 否则可能会导致冲突。
