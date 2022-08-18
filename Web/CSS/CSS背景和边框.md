---
title: CSS背景和边框
author: chaizz
date: 2022-8-18
tags: CSS
photo: ["https://tc.chaizz.com/760e75941d1511edb5630242ac190002.png"]

---

​          

<!--more-->

# CSS背景和边框

设计CSS的背景和边框，可以是CSS的样式更具有多样性。CSS背景可以做哪些不同的事情？

## 1 背景

### 1.1 颜色背景：`background-color`

定义了CSS中任何元素的背景颜色，属性接受的是有效的color值，==背景会扩展到元素的内容和内边距的下面==。

![](https://tc.chaizz.com/tc/Snipaste_2022-08-18_09-57-45.png)

```css
.box {
  background-color: #567895;
}

h2 {
  background-color: black;
  color: white;
}
span {
  background-color: rgba(255,255,255,.5);
}
```

```html
<div class="box">
  <h2>Background Colors</h2>
  <p>Try changing the background <span>colors</span>.</p>
</div>
```

### 1.2 图片背景：`background-image`

该属性允许在元素的背景中显示图像，但是要注意的是：大图不会缩小以适应标签方框，而小图则是平铺

填充方框。**如果除了背景图像外，还指定了背景颜色，则图像将颜色覆盖。**

```css
.a, .b {
    width: 400px;
    height: 200px;
    border: black solid 1px;
}

.a {
    background-color: red;
    background-image: url("9.png");

}

.b {
    background-image: url("400.jpeg");
}
```

```html
<div class="wrapper">
    <div class="box a"></div>
    <div class="box b"></div>
</div>
```

![](https://tc.chaizz.com/tc/Snipaste_2022-08-18_10-14-47.png)

#### 1.2.1 控制背景的平铺行为：`background-repeat`

- `no-repeat` 不重复。
- `repeat-x` 水平重复。
- `repeat-y` 垂直重复。
- `repeat` 在两个方向重复。

#### 1.2.2 调整背景图像的大小： `background-size`

可以设置长度（100px 200px），百分比。

- `cover`：浏览器将图像等比例放大，和元素盒子一样大，但是当图片尺寸和盒子尺寸不一致是往往会跳出盒子。

- `contain`：浏览器使图像的大小适应盒子内，如果图像的长宽比与盒子的长宽比不同，则可能在图像的任何一边或顶部和底部出现间隙。

#### 1.2.3 背景图像定位：`background-position`

设置背景图像显示在其应用到的盒子中的位置，在盒子中坐标系（0，0）位于盒子的左上角。默认额的背景图像位置为（0,0）。

-  `background-position` 是 `background-position-x` 和 `background-position-y`的简写，它们允许您分别设置不同的坐标轴的值。

#### 1.2.4 背景附加：`background-attachment`

==此属性只有在有内容要滚动时才会有效果==。

它可以接受以下值：[示例](https://mdn.github.io/learning-area/css/styling-boxes/backgrounds/background-attachment.html)

- `scroll`: 只有在页面滚动时背景图像在会滚动。如果滚动了元素内容，则背景不会移动。实际上，背景被固定在页面的相同位置，所以它会随着页面的滚动而滚动。
- `fixed`: 使元素的背景固定在页面的某个位置上，这样当页面或元素内容滚动时，它都不会滚动。它将始终保持在屏幕上相同的位置。
- `local`: 使用该值元素滚动和页面滚动都会导致背景图像滚动。

### 1.3 渐变背景

当渐变用于背景时，也可以使用像图像一样的 `background-image`属性设置。

![](C:\Users\LHKJ0\Pictures\WEB\CSS\Snipaste_2022-08-18_10-56-41.png)s

使用`background`可以设置全部的属性，但是在设置属性时有一些[规则](https://developer.mozilla.org/zh-CN/docs/Web/CSS/background)：

- `background-color` 只能在逗号之后指定。
- `background-size` 值只能包含在背景位置之后，用'/'字符分隔，例如：`center/80%`。

例如指定多个 `background-image` 的值时，用逗号分隔每个值。

```css
background-image: url(image1.png), url(image2.png), url(image3.png), url(image4.png);
background-repeat: no-repeat, repeat-x, repeat;
background-position: 10px 20px,  top right;
```

不同的属性值， 与其他属性中相同位置的值匹配。

## 2 边框

使用 border属性设置 CSS 中盒子设置边框的颜色、宽度和样式。 

### 2.1 边框样式：`border-style` 

关键字用于描述边框样式。它可以有以下取值：[示例](https://developer.mozilla.org/zh-CN/docs/Web/CSS/border-style)

| `none`   | 和关键字 `hidden` 类似，不显示边框。在这种情况下，如果没有设定背景图片，[`border-width`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/border-width) 计算后的值将是 `0`，即使先前已经指定过它的值。在单元格边框重叠情况下，`none` 值优先级最低，意味着如果存在其他的重叠边框，则会显示为那个边框。 |
| :------- | :----------------------------------------------------------- |
| `hidden` | 和关键字 `none` 类似，不显示边框。在这种情况下，如果没有设定背景图片，[`border-width`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/border-width) 计算后的值将是 `0`，即使先前已经指定过它的值。在单元格边框重叠情况下，`hidden` 值优先级最高，意味着如果存在其他的重叠边框，边框不会显示。 |
| `dotted` | 显示为一系列圆点。标准中没有定义两点之间的间隔大小，视不同实现而定。圆点半径是 [`border-width`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/border-width) 计算值的一半。 |
| `dashed` | 显示为一系列短的方形虚线。标准中没有定义线段的长度和大小，视不同实现而定。 |
| `solid`  | 显示为一条实线。                                             |
| `double` | 显示为一条双实线，宽度是 [`border-width`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/border-width) 。 |
| `groove` | 显示为有雕刻效果的边框，样式与 `ridge` 相反。                |
| `ridge`  | 显示为有浮雕效果的边框，样式与 `groove` 相反。               |
| `inset`  | 显示为有陷入效果的边框，样式与 `outset` 相反。当它指定到 [`border-collapse`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/border-collapse) 为 `collapsed` 的单元格时，会显示为 `groove` 的样式。 |
| `outset` | 显示为有突出效果的边框，样式与 `inset` 相反。当它指定到 [`border-collapse`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/border-collapse) 为 `collapsed` 的单元格时，会显示为 `ridge` 的样式。 |





































































































