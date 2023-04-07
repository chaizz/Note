---
title: JS笔记之DOM(三)
author: chaizz
date: 2023-4-3
tags: JavaScript
photo: ["https://tc.chaizz.com/ec55444c4a1211edac740242ac190002.png"]
---

​         

<!--more-->

JS笔记之DOM(三)

通过JS实时的创建DOM元素，是实时更新页面的关键。

创建元素

创建元素有两种方法：
```js
// 用给定的标签创建一个新 元素节点（element node）：
let div = document.createElement('div');

//用给定的文本创建一个 文本节点：
let textNode = document.createTextNode('Here I am');
```

将元素插入到HTML中

```js
// 将元素插入到body中。
document.body.append(div);

// 其他的插入方式
node.append(...nodes or strings) —— 在 node 末尾 插入节点或字符串，
node.prepend(...nodes or strings) —— 在 node 开头 插入节点或字符串，
node.before(...nodes or strings) —— 在 node 前面 插入节点或字符串，
node.after(...nodes or strings) —— 在 node 后面 插入节点或字符串，
node.replaceWith(...nodes or strings) —— 将 node 替换为给定的节点或字符串。
```

