---
title: Typora设置七牛云图床
author: chaizz
date: 2021-09-26
tags: Typora
photos: ["https://tc.chaizz.com/tc/Snipaste_2021-09-26_19-43-27.png"]
---

​             

<!--more-->

> Markdown是一种[轻量级标记语言](https://baike.baidu.com/item/轻量级标记语言/52671915)，创始人为约翰·格鲁伯（英语：John Gruber）。 它允许人们使用易读易写的[纯文本格式](https://baike.baidu.com/item/纯文本格式/9862288)编写文档，然后转换成有效的[XHTML](https://baike.baidu.com/item/XHTML/316621)（或者HTML）文档。这种语言吸收了很多在电子邮件中已有的纯文本标记的特性。
>
> 由于Markdown的轻量化、易读易写特性，并且对于图片，图表、数学式都有支持，许多网站都广泛使用Markdown来撰写帮助文档或是用于论坛上发表消息。 如[GitHub](https://baike.baidu.com/item/GitHub/10145341)、[Reddit](https://baike.baidu.com/item/Reddit/1272010)、[Diaspora](https://baike.baidu.com/item/Diaspora/10726893)、[Stack Exchange](https://baike.baidu.com/item/Stack Exchange/13777796)、[OpenStreetMap](https://baike.baidu.com/item/OpenStreetMap/3171606) 、[SourceForge](https://baike.baidu.com/item/SourceForge/6562141)、[简书](https://baike.baidu.com/item/简书/5782216)等，甚至还能被使用来撰写[电子书](https://baike.baidu.com/item/电子书/346054)

## 1 设置前提

注册七牛云账号并获得一下信息：

- 秘钥AK ：virqOxxxxxxxxxxxxxxxxxxxxxxxxMnNIK
- 秘钥SK：dDFGSasdfxxxxxxxxxxxxxxxxxxxiZiOVu
- 存储空间名称：cz-tuchuang
- 自定义域名：https://tc.chaizz.com
- 存储区域代码：我的是华北  所以就是 ：z1

存储区域对应代码图片

![](https://tc.chaizz.com/tc/Snipaste_2021-09-26_19-22-09.png)

## 2 开始设置

打开Typora  偏好设置 --> 图像 --> 插入图片时... --> 上传服务 --> 选择 上传图片。

打开Typora  偏好设置 --> 图像 --> 上传服务设定 --> 上传服务 --> 选择 PicGo-Core (command line)。

点击**下载或更新**按钮，会提示下载一个PicGo插件，等待下载完成点击**打开配置文件**，打开以后默认是空的。复制下面代码覆盖打开的文件。


```json
{
	"picBed": {
		"uploader": "qiniu",
		"qiniu": {
			"accessKey": "virqOxxxxxxxxxxxxxxxxxIK",  // 你的七牛云AK
			"secretKey": "dDFGSxxxxxxxxxxxxxxxxxVu",  // 你的七牛云SK
			"bucket": "cz-tuchuang",                  // 你的七牛云存储空间名
			"url": "https://tc.chaizz.com",           // 你的七牛云自定义域名
			"area": "z1",                             // 存储区域编号
			"options": "",                            // 网址后缀,可不填
			"path": "tc/",                            // 自定义存储路径，比如 img/ 可不填
			}
		},
	"picgoPlugins": {}
}

```


接下来点击 **验证图片上传选项** 按钮。上传成功会提示图下图所示：


![](https://tc.chaizz.com/tc/Snipaste_2021-09-26_19-35-24.png)

点击OK ，重启Typora上传图片即可上传到七牛云了。

