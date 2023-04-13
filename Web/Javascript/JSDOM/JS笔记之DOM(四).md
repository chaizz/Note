---
title: JS笔记之DOM(四)
author: chaizz
date: 2023-4-13
tags: JavaScript
photo: ["https://tc.chaizz.com/ec55444c4a1211edac740242ac190002.png"]
---

​         

<!--more-->

# JS笔记之DOM(四)

在JS中我们常常需要控制元素的大小，以及位置，特别是我们需要定位或者移动某些元素时。我们常见的元素的几何属性包括：内边距、边框、滚动等功能。



## 1 [offsetParent，offsetLeft/Top](https://zh.javascript.info/size-and-scroll#offsetparentoffsetlefttop)

![image-20230413205407832](https://tc.chaizz.com/tc/image-20230413205407832.png)

这些属性很少使用。

`offsetParent` 是最接近的祖先（ancestor），在浏览器渲染期间，它被用于计算坐标。

最近的祖先为下列之一：

1. CSS 定位的（`position` 为 `absolute`、`relative`、`fixed` 或 `sticky`），
2. 或 `<td>`，`<th>`，`<table>`，
3. 或 `<body>`。

属性 `offsetLeft/offsetTop` 提供相对于 `offsetParent` 左上角的 x/y 坐标。他返回的是一个值，不带单位。

有以下几种情况下，`offsetParent` 的值为 `null`：

1. 对于未显示的元素（`display:none` 或者不在文档中）。
2. 对于 `<body>` 与 `<html>`。
3. 对于带有 `position:fixed` 的元素。



## 2 [offsetWidth/Height](https://zh.javascript.info/size-and-scroll#offsetwidthheight)

![image-20230413205347622](https://tc.chaizz.com/tc/image-20230413205347622.png)

这两个属性是元素的本身，它们提供了元素的“外部” width/height。就是元素的大小，包含边框。

当元素既有内边距和边框时，offsetWidth/Height 的值就是: 元素本身大小+内边距+边框。

如果一个元素（或其任何祖先）具有 `display:none` 或不在文档中，则所有几何属性均为零（或 `offsetParent` 为 `null`）。

所以我们可以检查一个元素是否被隐藏：

```js
function isHidden(elem) {
    return !elem.offsetWidth && !elem.offsetHeight;
}
```

请注意，对于会展示在屏幕上，但大小为零的元素，它们的 `isHidden` 返回 `true`。



## 3 [clientTop/Left](https://zh.javascript.info/size-and-scroll#clienttopleft)

![image-20230413205317632](https://tc.chaizz.com/tc/image-20230413205317632.png)

在元素的内部有边框，可以使用clientTop/Left 来测量。准确的说这些属性，不是边框的width/height，而是内侧与外侧的相对坐标。

这是因为当文档的方向是从右到左显示时，此时如果出现滚动条，那么滚动条就在左边显示，此时 `clientLeft` 则包含了滚动条的宽度。所以`clientLeft` 的值是要加上滚动条的宽度。



## 4 [clientWidth/Height](https://zh.javascript.info/size-and-scroll#clientwidthheight)

![image-20230413205254196](https://tc.chaizz.com/tc/image-20230413205254196.png)



这两个属性时边框内部的区域的大小。它包括了内容的大小和内边距的大小。但是不包括滚动条。

**如果元素没有内边距，那么这两个属性就是内容的宽度和高度。**



## 5 [scrollWidth/Height](https://zh.javascript.info/size-and-scroll#scrollwidthheight)

这两个属性就像 `clientWidth/clientHeight`，但它们还包括滚动出（隐藏）的部分。

![image-20230413205736320](https://tc.chaizz.com/tc/image-20230413205736320.png)

一个案例就是我们可以将隐藏的元素全部展开：

```js
// 将元素展开（expand）到完整的内容高度
element.style.height = `${element.scrollHeight}px`;
```





## 6 [scrollLeft/scrollTop](https://zh.javascript.info/size-and-scroll#scrollleftscrolltop)

这两个属性是元素的隐藏、滚动部分的 width/height。也就是说在一个上下滚动的框中，scrollTop的值就是元素的内容往上滚动的多少。

![image-20230413210248597](https://tc.chaizz.com/tc/image-20230413210248597.png)

大多数的几何属性是只读的，但是scrollLeft/scrollTop是可以修改的，所以当我们修改scrollTop时，代表着内容向上滚动了，将 scrollTop 设置为 0 或一个大的值，例如 1e9，将会使元素滚动到顶部/底部。



## 7 不要从 CSS 中获取 width/height

首先为什么不要从css中获取宽高，原因有两个：

1. 首先，CSS `width/height` 取决于另一个属性：`box-sizing`，它定义了“什么是” CSS 宽度和高度。出于 CSS 的目的而对 `box-sizing` 进行的更改可能会破坏此类 JavaScript 操作。

2. 其次，CSS 的 `width/height` 可能是 `auto`，例如内联（inline）元素。



还有另一个原因：滚动条。有时，在没有滚动条的情况下代码工作正常，当出现滚动条时，代码就出现了 bug，因为在某些浏览器中，滚动条会占用内容的空间。因此，可用于内容的实际宽度小于 CSS 宽度。而 clientWidth/clientHeight 则会考虑到这一点。

但是，使用 getComputedStyle(elem).width 时，情况就不同了。某些浏览器（例如 Chrome）返回的是实际内部宽度减去滚动条宽度，而某些浏览器（例如 Firefox）返回的是 CSS 宽度（忽略了滚动条）。这种跨浏览器的差异是不使用 getComputedStyle 而依靠几何属性的原因。





## 8 总结

元素具有以下几何属性：

- `offsetParent` —— 是最接近的 CSS 定位的祖先，或者是 `td`，`th`，`table`，`body`。
- `offsetLeft/offsetTop` —— 是相对于 `offsetParent` 的左上角边缘的坐标。
- `offsetWidth/offsetHeight` —— 元素的“外部” width/height，边框（border）尺寸计算在内。
- `clientLeft/clientTop` —— 从元素左上角外角到左上角内角的距离。对于从左到右显示内容的操作系统来说，它们始终是左侧/顶部 border 的宽度。而对于从右到左显示内容的操作系统来说，垂直滚动条在左边，所以 `clientLeft` 也包括滚动条的宽度。
- `clientWidth/clientHeight` —— 内容的 width/height，包括 padding，但不包括滚动条（scrollbar）。
- `scrollWidth/scrollHeight` —— 内容的 width/height，就像 `clientWidth/clientHeight` 一样，但还包括元素的滚动出的不可见的部分。
- `scrollLeft/scrollTop` —— 从元素的左上角开始，滚动出元素的上半部分的 width/height。

除了 `scrollLeft/scrollTop` 外，所有属性都是只读的。如果我们修改 `scrollLeft/scrollTop`，浏览器会滚动对应的元素。
