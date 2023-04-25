---
title: CSS盒模型
author: chaizz
date: 2022-8-17
tags: CSS
photo: ["https://origin.chaizz.com/760e75941d1511edb5630242ac190002.png"]
---

​          

<!--more-->

# CSS盒模型

在CSS中所有的元素都被一个个的*“盒子”*包围着，CSS的布局处理元素排列正式通过盒子来实现的。

在CSS中有两种盒子是被广泛使用的：块级（block）盒子和内联（inline）盒子。这两种盒子会在页面上表现出两种不同的行为。

## 1 块级盒子

- 盒子会在内联的方向上扩展并占据父容器的在该方向上的所有可用空间，大多数情况下意味着盒子会和父容器一样宽。
- 每个盒子都会换行。
- `width`和`hright`属性可以发挥作用。
- 内边距（padding）外边距（margin）和边框（border）会将其他的元素从当前格子周围推开。(相邻两个块级盒子之间的垂直间距会遵循外边距折叠原则被折叠。)

除非特殊指定，诸如`h1`等和段落`p`默认都是块级的盒子。

## 2 内联盒子

- 内联盒子不会产生换行。
- `width`和`hright`属性不起作用。
- 垂直方向的内边距（padding）外边距（margin）和边框（border）==会被应用==，但是==不会==把其他的处于`inline`状态的盒子推开。
- 水平方向的内边距（padding）外边距（margin）和边框（border）==会被应用==，==会==把其他的处于`inline`状态的盒子推开。

针对块级盒子和内联盒子可以使用`display`属性设置`block`和`inline`。

## * 补充

以上叙述中是CSS盒模型的外部显示类型，决定盒子是内联盒子还是块级盒子。同样盒模型也有一个内部显示类型，它决定了盒子内部是如何布局的，默认是按照[正常文档流布局](https://developer.mozilla.org/zh-CN/docs/Learn/CSS/CSS_layout/Normal_Flow)，但是可以通过`flex`的`display`属性来更改内部显示类型。

例如：如果设置 `display: flex`，在一个元素上，外部显示类型是 `block`，但是内部显示类型修改为 `flex`。==该盒子的所有直接子元素都会成为 flex 元素==。会根据==[弹性盒子](https://developer.mozilla.org/zh-CN/docs/Learn/CSS/CSS_layout/Flexbox)==规则进行布局。

块级和内联是web上默认的行为，它们被称为正常文档流，如果没有其他的说明，盒子的默认布局就是块级或者内联。

## 3 盒模型的组成部分

- Content Box：这个区域使用来显示内容，大小可以通过设置`width`和`height`。
- Padding Box：包围在内容区域外部的空白区域，大小可以通过`padding`设置。
- Border Box：盒子的边框区域，大小通过`border`设置。
- Margin Box：盒子的最外围区域，是盒子和其他元素之间的空白区域，大小通过`margin`设置。

如下图：

![](https://origin.chaizz.com/tc/box-model.png)

## 4 标准盒模型

在标准盒模型中，给盒子设置width和height，实际设置的是content box padding和border再加上一起设置的width和height再决定了盒子的大小。

假设定义了 `width`, `height`, `margin`, `border`和`padding`:

```css
.box {
  width: 350px;
  height: 150px;
  margin: 25px;
  padding: 25px;
  border: 5px solid black;
}
```



计算出的模型宽度 = 410px(350 + 25 + 25 + 5 + 5)，高度 = 210px (150 + 25 + 25 + 5 + 5)，padding 加 border 再加 content box。

![](C:\Users\LHKJ0\Pictures\WEB\CSS\standard-box-model.png)

> 注意：margin不计入实际大小，但是它会影响盒子实际占用的空间，影响的是盒子外部的空间，盒子的范围到边框为止，不会延伸到margin。



## 5 替代盒模型

这个模型所有的宽度都是可见宽度，所以内容的宽度是总宽度减去边框的宽度和内边距的宽度。

默认浏览器会使用标准盒模型，需要使用替代模型需要设置 `box-sizing:border-box`。如果想让所有元素都是用替代模型，只需要在html元上指定`box-sizing`，然后设置所有元素继承该属性。

```css
html {
  box-sizing: border-box;
}
*, *::before, *::after {
  box-sizing: inherit;
}
```

## 6 盒子模型的属性解析

### 6.1 外边距（margin）

外边距是盒子周围看不到的一些空间，他会把其他的元素从盒子旁边推开，外边距的属性可以为正也可以为负，设置负值会导致和其他的内容重叠。无论是使用标准盒子模型还是替代盒子模型，外边距总是在计算可见部分后额外的添加。

可以使用`margin`设置四个反向的内容，也可以分别使用四个独立的属性设置：`margin-top`、`margin-right`、`margin-left`、`margin-right`。

```css
div {
    margin-top: 10px;        /* 绝对长度 */
	margin-top: 1em;         /*相对于字体大小 */
	margin-top: 5%;          /*相对于最相邻的父级元素块（block）的宽度*/
}
```



> 外边距折叠：如果有两个外边距相接的元素，这些外边距将合并为一个外边距，即最大的那个外边距的大小。
>
> 有设定[float](https://developer.mozilla.org/zh-CN/docs/Web/CSS/float)和[position=absolute](https://developer.mozilla.org/zh-CN/docs/Web/CSS/position#absolute)的元素不会产生外边距重叠行为。



### 6.2 内边距（padding）

内边距位于边框和内容之间，==且不能有负数的形式==，应用于元素的任何背景都将显示在内边距后面，内边距通常用于将内容推离边框。我们可以使用[`padding`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/padding)简写属性控制元素所有边，或者每边单独使用等价的普通属性：`padding-top`、`padding-right`、`padding-bottom`、`padding-left`。

### 6.3 边框（border）

边框是在边距和填充框之间绘制的。如果您正在使用标准的盒模型，边框的大小将添加到框的宽度和高度。如果您使用的是替代盒模型，那么边框的大小会使内容框更小，因为它会占用一些可用的宽度和高度。

可以使用[`border`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/border)属性一次设置所有四个边框的宽度、颜色和样式。

分别设置每边的宽度、颜色和样式，可以使用：

[`border-top`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/border-top)、[`border-right`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/border-right)、[`border-bottom`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/border-bottom)、[`border-left`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/border-left)

设置所有边的颜色、样式或宽度，请使用以下属性：[`border-width`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/border-width)、[`border-style`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/border-style)、[`border-color`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/border-color)

设置单边的颜色、样式或宽度，可以使用最细粒度的普通属性之一：[`border-top-width`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/border-top-width)、[`border-top-style`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/border-top-style)、[`border-top-color`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/border-top-color)、[`border-right-width`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/border-right-width)、[`border-right-style`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/border-right-style)[`border-right-color`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/border-right-color)、[`border-bottom-width`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/border-bottom-width)、[`border-bottom-style`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/border-bottom-style)、[`border-bottom-color`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/border-bottom-color)、[`border-left-width`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/border-left-width)、[`border-left-style`、](https://developer.mozilla.org/zh-CN/docs/Web/CSS/border-left-style)[`border-left-color`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/border-left-color)



## 7 盒子模型和内联盒子

以上所有的方法都完全适用于块级盒子，有些属性也可以应用于内联盒子。在内联合中应用宽度、高度、边距、边框和内边距时，宽度和高度是被忽略的，外边距、内边距和边框是生效的。但是他们不会改变其他内容与盒子的关系，因此内边距和边框会与段落中的其他单词重叠。

![](C:\Users\LHKJ0\Pictures\WEB\CSS\Snipaste_2022-08-17_17-29-30.png)

为了解决以上的问题可以使用：display: inline-block。display 有一个特殊的值，它在内联和块之间提供了一个中间状态。这对于以下情况非常有用：不希望一个元素切换到新行，但希望它可以设定宽度和高度，并避免上面看到的重叠。

一个元素使用 `display: inline-block`，实现我们需要的块级的部分效果：

- 设置`width` 和`height` 属性会生效。
- `padding`, `margin`, 以及`border` 会推开其他元素。

但是，它不会跳转到新行，如果显式添加 `width` 和 `height` 属性，它只会变得比其内容更大。





























