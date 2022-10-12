---
title: js笔记之Js代码调用策略
author: chaizz
date: 2022-10-12 17:40:18
tags: Javascript
photo: ["https://tc.chaizz.com/ec55444c4a1211edac740242ac190002.png"]
---

​          

<!--more-->

javascript代码位置和css一样，可以设置为内部样式、内联样式、外部样式。HTML代码渲染是由上而下加载，js代码的位置可能会导致js获取DOM元素时，无法获取未渲染的HTML标签，从而引发错误。

想要脚本调用的时候符合预期，需要解决一系列问题。

例如当js代码处于文档头处，解析HTML文档体之前。这样做是有隐患的，需要使用一些结构来避免错误发生。

```js
document.addEventListener("DOMContentLoaded", function () {
    . . .
});
```

这是一个事件监听器，他监听浏览器的**DOMContentLoaded**事件，即HTML加载、解释完毕事件。事件将触发 ... 的代码，从而避免了错误发生。