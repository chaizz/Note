---
title: CSS转换
author: chaizz
date: 2022-8-24
tags: CSS
photo: ["https://tc.chaizz.com/760e75941d1511edb5630242ac190002.png"]
---

​                 

<!--more-->

CSS 转换

转换可以对元素进行移动、缩放、转动、拉伸。转换的效果是让某个元素改变形状，大小和位置。可以使用2D和3D来转换元素，

2D转换

平移 translate

根据左(X轴)和顶部(Y轴)位置给定的参数，从当前元素位置移动。

```css
div {
    transform: translate(50px,100px);
    -ms-transform: translate(50px,100px); /* IE 9 */
    -webkit-transform: translate(50px,100px); /* Safari and Chrome */
}
```

以上css规则：是从左边元素移动50个像素，并从顶部移动100像素。

旋转 rotate

顺时针旋转的角度。负值是逆时针旋转。

```css
div {
    transform: rotate(30deg);
    -ms-transform: rotate(30deg); /* IE 9 */
    -webkit-transform: rotate(30deg); /* Safari and Chrome */
}
```

以上css规则：将元素顺时针旋转30度。

缩放 scale

增加或减少的大小，取决于宽度（X轴）和高度（Y轴）的参数。

```css
div {
    -ms-transform:scale(2,3); /* IE 9 */
    -webkit-transform: scale(2,3); /* Safari */
    transform: scale(2,3); /* 标准语法 */
}
```

以上css规则：将元素转变宽度为原来的大小的2倍，和其原始大小3倍的高度。

倾斜 skew

包含两个参数值，分别表示X轴和Y轴倾斜的角度，如果第二个参数为空，则默认为0，参数为负表示向相反方向倾斜。

```css
div {
    transform: skew(30deg,20deg);
    -ms-transform: skew(30deg,20deg); /* IE 9 */
    -webkit-transform: skew(30deg,20deg); /* Safari and Chrome */
}
```

以上css规则：将元素在X轴和Y轴上倾斜20度30度。



以上方法的缩写 matrix

matrix 方法有六个参数，包含旋转，缩放，移动（平移）和倾斜功能。

表示以下函数：matrix( scaleX(), skewY(), skewX(), scaleY(), translateX(), translateY() )

```css
div {
    transform:matrix(0.866,0.5,-0.5,0.866,0,0);
    -ms-transform:matrix(0.866,0.5,-0.5,0.866,0,0); /* IE 9 */
    -webkit-transform:matrix(0.866,0.5,-0.5,0.866,0,0); /* Safari and Chrome */
}
```



























