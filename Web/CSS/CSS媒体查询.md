---
title: CSS媒体查询
author: chaizz
date: 2022-8-22
tags: CSS
photo: ["https://tc.chaizz.com/760e75941d1511edb5630242ac190002.png"]
---

​          

<!--more-->



# CSS媒体查询



CSS 媒体查询为你提供了一种应用 CSS 的方法，仅在浏览器和设备的环境与你指定的规则相匹配的时候 CSS 才会真的被应用

媒体查询是响应式 Web 设计的关键部分，因为它允许你按照视口的尺寸创建不同的布局，不过它也可以用来探测和你的站点运行的环境相关联的其它条件，比如用户是在使用触摸屏还是鼠标。



## 1 媒体查询基础

最简单的媒体查询语法看起来是像这样的：

```css
@media media-type and (media-feature-rule) {
  /* CSS rules go here */
}
```

它由以下部分组成：

- 一个媒体类型，告诉浏览器这段代码是用在什么类型的媒体上的（例如印刷品或者屏幕）；
- 一个媒体表达式，是一个被包含的 CSS 生效所需的规则或者测试；
- 一组 CSS 规则，会在测试通过且媒体类型正确的时候应用。



## 2 媒体类型

- all：适用于所有设备。
- print：适用于在打印预览模式下在屏幕上查看的分页材料和文档。
- screen：主要用于屏幕。
- speech：主要用于语音合成器



## 3 媒体特征规则

### 3.1宽和高 width height

为了建立响应式设计（已经广受浏览器支持），我们一般最常探测的特征是视口宽度，而且我们可以使用min-width、max-width和width媒体特征，在视口宽度大于或者小于某个大小——或者是恰好处于某个大小——的时候，应用 CSS。

在实践中一般比较常用的是 min- 和 max- 

```css
@media screen and (max-width: 400px) {
    body {
        color: blue;
    }
}
```



### 3.2 朝向 orientation

可以用它测得竖放（portrait）模式 和横放（landscape）模式。

```css
@media (orientation: landscape) {
    body {
        color: rebeccapurple;
    }
}
```

标准的桌面视图是横放朝向的，在这种朝向上能够表现良好的设计，在处于竖放模式的手机或平板电脑上可能不会表现得这么好。对朝向的测试可以帮你建立一个为竖放设备优化的布局。



### 3.3 使用可指点设备（可悬浮在元素上）

```css
@media (hover: hover) {
    body {
        color: rebeccapurple;
    }
}
```



## 4 复杂的媒体查询

使用与、或、非组合上面的媒体查询逻辑。

规则：body 的文字只会在viewport至少为 400 像素宽，且设备横放时应用变为蓝色。

```css
@media screen and (min-width: 400px) and (orientation: landscape) {
    body {
        color: blue;
    }
}
```

有一组查询，且要其中的任何一个都可以匹配的话，那么你可以使用逗号分开这些查询。

规则：body会在viewport至少为 400 像素宽的时候**或者**设备处于横放状态的时候应用变为蓝色

```css
@media screen and (min-width: 400px), screen and (orientation: landscape) {
    body {
        color: blue;
    }
}
```

使用not`操作符让整个媒体查询失效。这就直接反转了整个媒体查询的含义。

```css
@media not all and (orientation: landscape) {
    body {
        color: blue;
    }
}
```

