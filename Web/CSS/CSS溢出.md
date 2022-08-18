---
title: CSS溢出
author: chaizz
date: 2022-8-17
tags: CSS
photo: ["https://tc.chaizz.com/760e75941d1511edb5630242ac190002.png"]
---

​          

<!--more-->

CSS溢出

在CSS中一切皆盒子，我们可以设置盒子的大小，但是当我们的内容大于盒子的大小的时候，就会导致内容溢出。

控制溢出的几种方式

overflow

默认值：`visible`，默认情况下溢出是可见的。

如果想让溢出的元素裁减掉，可以使用`hidden`。

既不裁剪也不希望溢出，`scroll` 更合适，他会在原本的盒子上加一个滚动条，既保证盒子的大小，也不会将数据裁减掉。

```css
.box {
  border: 1px solid #333333;
  width: 200px;
  height: 100px;
  overflow: scroll;
}
```

```html
<div class="box">This box has a height and a width. This means that if there is too much content to be displayed within the assigned height, there will be an overflow situation. If overflow is set to hidden then any overflow will not be visible.</div>

<p>This content is outside of the box.</p>
```

![](https://tc.chaizz.com/tc/Snipaste_2022-08-18_14-22-00.png)

以上的情况确实显示滚动条，但是横向也显示了滚动条，如果只希望在Y轴上显示可以使用`overflow-y: scroll`。同理x轴：`overflow-x: scroll`。

使用`overflow: auto`，由浏览器决定是否显示滚动条。桌面浏览器一般仅仅会在有足以引起溢出的内容的时候这么做。













































