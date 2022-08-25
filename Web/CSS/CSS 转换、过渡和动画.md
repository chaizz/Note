---
title: CSS 转换、过渡和动画
author: chaizz
date: 2022-8-24
tags: CSS
photo: ["https://tc.chaizz.com/760e75941d1511edb5630242ac190002.png"]
---

​                 

<!--more-->

# CSS 转换、过渡和动画

转换可以对元素进行移动、缩放、转动、拉伸。转换的效果是让某个元素改变形状，大小和位置。可以使用2D和3D来转换元素，

## 1 2D转换

### 1.1 平移 translate

根据左(X轴)和顶部(Y轴)位置给定的参数，从当前元素位置移动。

```css
div {
    transform: translate(50px,100px);
    -ms-transform: translate(50px,100px); /* IE 9 */
    -webkit-transform: translate(50px,100px); /* Safari and Chrome */
}
```

以上css规则：是从左边元素移动50个像素，并从顶部移动100像素。

### 1.2 旋转 rotate

顺时针旋转的角度。负值是逆时针旋转。

```css
div {
    transform: rotate(30deg);
    -ms-transform: rotate(30deg); /* IE 9 */
    -webkit-transform: rotate(30deg); /* Safari and Chrome */
}
```

以上css规则：将元素顺时针旋转30度。

### 1.3 缩放 scale

增加或减少的大小，取决于宽度（X轴）和高度（Y轴）的参数。

```css
div {
    -ms-transform:scale(2,3); /* IE 9 */
    -webkit-transform: scale(2,3); /* Safari */
    transform: scale(2,3); /* 标准语法 */
}
```

以上css规则：将元素转变宽度为原来的大小的2倍，和其原始大小3倍的高度。

### 1.4 倾斜 skew

包含两个参数值，分别表示X轴和Y轴倾斜的角度，如果第二个参数为空，则默认为0，参数为负表示向相反方向倾斜。

```css
div {
    transform: skew(30deg,20deg);
    -ms-transform: skew(30deg,20deg); /* IE 9 */
    -webkit-transform: skew(30deg,20deg); /* Safari and Chrome */
}
```

以上css规则：将元素在X轴和Y轴上倾斜20度30度。



### 1.5 以上方法的缩写 matrix

matrix 方法有六个参数，包含旋转，缩放，移动（平移）和倾斜功能。

表示以下函数：matrix( scaleX(), skewY(), skewX(), scaleY(), translateX(), translateY() )

```css
div {
    transform:matrix(0.866,0.5,-0.5,0.866,0,0);
    -ms-transform:matrix(0.866,0.5,-0.5,0.866,0,0); /* IE 9 */
    -webkit-transform:matrix(0.866,0.5,-0.5,0.866,0,0); /* Safari and Chrome */
}
```



## 2 CSS 过渡

### 2.1 transition 使某种想过转变到另一种效果。

- transition-property 应用过渡的CSS属性。
- transition-duration 过渡的CSS时间，默认是0。
- transition-timing-function 规定过渡效果的时间曲线。默认是 "ease"。
- transition-delay	规定过渡效果何时开始。默认是 0 （立即开始）。

```css
div {
    transition-property: width;
    transition-duration: 1s;
    transition-timing-function: linear;
    transition-delay: 2s;
    /* Safari */
    -webkit-transition-property:width;
    -webkit-transition-duration:1s;
    -webkit-transition-timing-function:linear;
    -webkit-transition-delay:2s;
}
```

### 2.2 transition 以上属性简写

```css
div {
    transition: width 1s linear 2s;
    /* Safari */
    -webkit-transition:width 1s linear 2s;
}
```



## 3 CSS 动画

动画是使元素从一种样式逐渐变化为另一种样式的效果，可以改变任意多的样式任意多的次数。

使用百分比来规定变化发生的时间，或者使用关键字`from`和`to`等同于0%和100%。为了有更好的体验，应该使用定义0%和100%。

```css
div {
    animation: myfirst 5s;
    -webkit-animation: myfirst 5s; /* Safari 与 Chrome */
}

@keyframes myfirst {
    0%   {background: red;}
    25%  {background: yellow;}
    50%  {background: blue;}
    100% {background: green;}
}
 
@-webkit-keyframes myfirst /* Safari 与 Chrome */ {
    0%   {background: red;}
    25%  {background: yellow;}
    50%  {background: blue;}
    100% {background: green;}
}
```

### 3.1 animation 相关属性

- @keyframes	规定动画规则。
- animation-name	规定 @keyframes 动画的名称。	
- animation-duration	规定动画完成一个周期所花费的秒或毫秒。默认是 0。	
- animation-timing-function	规定动画的速度曲线。默认是 "ease"。	
- animation-fill-mode	规定当动画不播放时（当动画完成时，或当动画有一个延迟未开始播放时），要应用到元素的样式。	
- animation-delay	规定动画何时开始。默认是 0。	
- animation-iteration-count	规定动画被播放的次数。默认是 1。	
- animation-direction	规定动画是否在下一周期逆向地播放。默认是 "normal"。	
- animation-play-state	规定动画是否正在运行或暂停。默认是 "running"

### 3.2 设置所有属性示例

```css
div {
    animation-name: myfirst;
    animation-duration: 5s;
    animation-timing-function: linear;
    animation-delay: 2s;
    animation-iteration-count: infinite;
    animation-direction: alternate;
    animation-play-state: running;
    /* Safari 与 Chrome: */
    -webkit-animation-name: myfirst;
    -webkit-animation-duration: 5s;
    -webkit-animation-timing-function: linear;
    -webkit-animation-delay: 2s;
    -webkit-animation-iteration-count: infinite;
    -webkit-animation-direction: alternate;
    -webkit-animation-play-state: running;
}
```

简写示例

```css
div {
    animation: myfirst 5s linear 2s infinite alternate;
    /* Safari 与 Chrome: */
    -webkit-animation: myfirst 5s linear 2s infinite alternate;
}
```



























































