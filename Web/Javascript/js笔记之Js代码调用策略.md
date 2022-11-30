---
title: JS笔记之JS代码调用策略
author: chaizz
date: 2022-10-12 17:40:18
tags: javaScript
photo: ["https://tc.chaizz.com/ec55444c4a1211edac740242ac190002.png"]
---

​          

<!--more-->

# JS代码调用策略

javaScript代码位置和css一样，可以设置为内部样式、内联样式、外部样式。HTML代码渲染是由上而下加载，js代码的位置可能会导致js获取DOM元素时，无法获取未渲染的HTML标签，从而引发错误。

想要脚本调用的时候符合预期，需要解决一系列问题。

例如当js代码处于文档头处，解析HTML文档体之前。这样做是有隐患的，需要使用一些结构来避免错误发生。

## 1 使用 DOMContentLoaded 解决

```js
document.addEventListener("DOMContentLoaded", function () {
    . . .
});
```

这是一个事件监听器，他监听浏览器的**DOMContentLoaded**事件，即HTML加载、解释完毕事件。事件将触发 ... 的代码，从而避免了错误发生。

## 2 使用 async 解决

在调用外部js代码时可以使用js的一项现代技术（async）来解决这一问题，他告知浏览器在遇到<javaScript> 元素时不用中断后续HTML内容的加载。

```html
<script src="script.js" async></script>
```

**注：** 外部脚本 async 属性可以解决调用顺序问题，但是async只适用于外部脚本。



一般解决此问题的旧方法是：把脚本元素放在底部，这样脚本就可以再HTML内容解析之后加载了。这种方案和上面 DOMContentLoaded 的问题是：==只有在所有 HTML DOM 加载完成后才开始脚本的加载/解析过程。对于有大量 javaScript 代码的大型网站，可能会带来显著的性能损耗。这也是 async 属性诞生的初衷。==

## 3 async 和 defer

上述的脚本阻塞问题实际有两种解决方案 —— `async` 和 `defer`。

浏览器遇到acync脚本时不会阻塞渲染HTML，而是直接下载然后运行，这样脚本的执行顺序就无法把控，仅仅是脚本不会阻止剩余页面的渲染。当页面和脚本之间独立，且不依赖于本页面的其它任何脚本时，`async` 是最理想的选择。

比如页面有多个脚本：

```html
<script async src="js/vendor/jquery.js"></script>

<script async src="js/script2.js"></script>

<script async src="js/script3.js"></script>
```

三者的调用顺序是不一定的，`jquery` 可能在 `script2` 和 `script3` 之后调用，如果这样的话，后两个脚本中依赖 `jquery` 的函数将产生错误，因为脚本运行时 `jquery` 尚未加载。

解决这一问题可使用 `defer` 属性，脚本将按照在页面中出现的顺序加载和运行：

```html
<script defer src="js/vendor/jquery.js"></script>

<script defer src="js/script2.js"></script>

<script defer src="js/script3.js"></script>
```

添加 `defer` 属性的脚本将按照在页面中出现的顺序加载，因此第二个示例可确保 `jquery.js` 必定加载于 `script2.js` 和 `script3.js` 之前，同时 `script2.js` 必定加载于 `script3.js` 之前。



## 4 总结：

如果脚本无需等待页面解析，且无依赖独立运行，那么应使用 `async`。

如果脚本需要等待页面解析，且依赖于其它脚本，调用这些脚本时应使用 `defer`，将关联的脚本按所需顺序置于 HTML 中。











​          
