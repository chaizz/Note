---
title: CSS样式化文本
author: chaizz
date: 2022-8-19
tags: CSS
photo: ["https://origin.chaizz.com/760e75941d1511edb5630242ac190002.png"]
---

​          

<!--more-->

## 1 CSS文本样式

### 1.1 字体栈

因为无法保证每个网页的上使用的字体的可用性，所以可以提供一个字体栈，这样浏览器就可以有多种选择。只需要使用`font-famioly`属性，其值用几个逗号分离的字体名称组成。使用方式如下：

```css
p {
  font-family: "Trebuchet MS", Verdana, sans-serif;
}
```

默认情况下，浏览器会检查第一个字体，在征集其中是否可用，如果不可用则会顺延到下一个字体，在进行检查以此类推，都不可用则会使用默认的字体。

> **备注：** 有一些字体名称不止一个单词，比如`Trebuchet MS` ，那么就需要用引号包裹。

### 1.2 字体大小

主要注意字体的单位

- px：将像素的值赋予文本，他是一个绝对的单位，任何情况下页面上的文本大小都是固定的。
- em：1em等于当前元素的父元素上设置的字体的大小，如果设置了大量的不同字体大小的嵌套元素， 可能会导致字体大小非常混乱。
- rem：1rem等于html中根元素的字体的大小（<html>），而不是父元素的字体大小，但是不支持IE8及以下版本。

标签的`font-size`属性是从根元素（<html>）继承的m，所以一切都是从根元素的开始，==浏览器的默认`font-size` 标准设置的值为 16px==。在根元素中的任何段落中的没有设置字体大小的元素都有一个默认值：16px，其他的元素有默认的代大小，比如`h1`的默认大小为2em，转化为像素为32px。

### 1.3 字体样式

`font-style`: 用来打开和关闭文本 italic (斜体)。可能的值如下 (你很少会用到这个属性，除非你因为一些理由想将斜体文字关闭斜体状态)：

- `normal`: 将文本设置为普通字体 (将存在的斜体关闭)
- `italic`: 如果当前字体的斜体版本可用，那么文本设置为斜体版本；如果不可用，那么会利用 oblique 状态来模拟 italics。
- `oblique`: 将文本设置为斜体字体的模拟版本，也就是将普通文本倾斜的样式应用到文本中。

`font-weight`: 设置文字的粗体大小。这里有很多值可选 (比如-light,-normal,-bold,-extrabold,-black, 等等), 不过事实上你很少会用到`normal`和`bold`以外的值：

- `normal`, `bold`: 普通或者**加粗**的字体粗细
- `lighter`, `bolder`: 将当前元素的粗体设置为比其父元素粗体更细或更粗一步。`100`–`900`: 数值粗体值，如果需要，可提供比上述关键字更精细的粒度控制。

`text-transform`: 允许你设置要转换的字体。值包括：

- `none`: 防止任何转型。
- `uppercase`: 将所有文本转为大写。
- `lowercase`: 将所有文本转为小写。
- `capitalize`: 转换所有单词让其首字母大写。
- `full-width`: 将所有字形转换成全角，即固定宽度的正方形，类似于等宽字体，允许拉丁字符和亚洲语言字形（如中文，日文，韩文）对齐。

`text-decoration`: 设置/取消字体上的文本装饰 (你将主要使用此方法在设置链接时取消设置链接上的默认下划线。) 可用值为：

- `none`: 取消已经存在的任何文本装饰。
- `underline`: 文本下划线。
- `overline`: 文本上划线
- `line-through`: 穿过文本的线。

`text-decoration`可以一次接受多个值 比如

```css
text-decoration: underline overline
```

`text-decoration`是一个缩写形式，它由`text-decoration-line`,`text-decoration-style`和`text-decoration-color`构成。你可以使用这些属性值的组合来创建有趣的效果，比如

```css
text-decoration: line-through red wavy
```

文字样式示例

```css
p {
    font-size: 1.4rem;
    color: red;
    font-style: normal;
    font-weight: lighter;
    font-family: Helvetica, Arial, sans-serif;
    text-decoration: line-through underline overline wheat;
}
```



### 1.4 文字阴影

使用`text-shadow`设置文字阴影。

```css
text-shadow: 4px 4px 5px red;
```

1. 阴影与原始文本的水平偏移，可以使用大多数的 CSS 单位 [length and size units](https://developer.mozilla.org/en-US/docs/Learn/CSS/Building_blocks/Values_and_units#length_and_size), 但是 px 是比较合适的。这个值必须指定。
2. 阴影与原始文本的垂直偏移;效果基本上就像水平偏移，除了它向上/向下移动阴影，而不是左/右。这个值必须指定。
3. 模糊半径 - 更高的值意味着阴影分散得更广泛。如果不包含此值，则默认为 0，这意味着没有模糊。可以使用大多数的 CSS 单位 [length and size units](https://developer.mozilla.org/en-US/docs/Learn/CSS/Building_blocks/Values_and_units#length_and_size).
4. 阴影的基础颜色，可以使用大多数的 CSS 颜色单位 [CSS color unit](https://developer.mozilla.org/en-US/docs/Learn/CSS/Building_blocks/Values_and_units#colors). 如果没有指定，默认为 `black`.

> **备注：** 正偏移值可以向右移动阴影，但也可以使用负偏移值来左右移动阴影，例如 `-1px -1px`。

字体还可以指定多个阴影：

```css
text-shadow: -1px -1px 1px #aaa,
             0px 4px 1px rgba(0,0,0,0.5),
             4px 4px 5px rgba(0,0,0,0.7),
             0px 0px 7px rgba(0,0,0,0.4);
```

### 1.5 文本布局

`text-align`

该属性用来控制文本如何和它所在的内容盒子对齐。可用值如下，并且在与常规文字处理器应用程序中的工作方式几乎相同：

- `left`: 左对齐文本。
- `right`: 右对齐文本。
- `center`: 居中文字
- `justify`: 使文本展开，改变单词之间的差距，使所有文本行的宽度相同。你需要仔细使用，它可以看起来很可怕。特别是当应用于其中有很多长单词的段落时。如果你要使用这个，你也应该考虑一起使用别的东西，比如 [`hyphens`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/hyphens)，打破一些更长的词语。

`line-height` 

该属性设置文本每行之间的高，可以接受大多数单位 [length and size units](https://developer.mozilla.org/en-US/docs/Learn/CSS/Building_blocks/Values_and_units#length_and_size)，不过也可以设置一个无单位的值，作为乘数，通常这种是比较好的做法。无单位的值乘以 [`font-size`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/font-size) 来获得 `line-height`。当行与行之间拉开空间，正文文本通常看起来更好更容易阅读。==推荐的行高大约是 1.5–2 (双倍间距)==。 所以要把我们的文本行高设置为字体高度的 1.5 倍，你可以使用这个：

```css
line-height: 1.5;
```



`letter-spacing` 和 `word-spacing`

以上两个值可以设置字母与字母之间的间距、或是单词与单词之间的间距。



## 2 css列表样式

### 2.1 列表特定样式

[`list-style-type`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/list-style-type) ：设置用于列表的项目符号的类型，例如无序列表的方形或圆形项目符号，或有序列表的数字，字母或罗马数字。

[`list-style-position`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/list-style-position) ：设置在每个项目开始之前，项目符号是出现在列表项内，还是出现在其外。

[`list-style-image`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/list-style-image) ：允许您为项目符号使用自定义图片，而不是简单的方形或圆形。（这个属性在控制项目符号的位置，大小等方面是有限的，最好使用`background`）

```css
ul {
  list-style-type: square;
  list-style-image: url(example.png);
  list-style-position: inside;
}
```

以上的三种属性，可以使用一个单独的属性`list-style`来设置，属性值可以任意顺序排列，可以设置一个，两个或者三个值（该属性的默认值为 disc, none, outside），如果指定了 type 和 image，如果由于某种原因导致图像无法加载，则 type 将用作回退。

```css
ul {
  list-style: square url(example.png) inside;
}
```

### 2.2 管理列表计数

在列表上进行不同的计数方式，例如设置`ol`标签的序号，正序倒序，指定数值。

[`start`](https://developer.mozilla.org/zh-CN/docs/Web/HTML/Element/ol#attr-start) 属性允许你从 1 以外的数字开始计数

```html
<ol start="4">
  <li>Toast pitta, leave to cool, then slice down the edge.</li>
  <li>Fry the halloumi in a shallow, non-stick pan, until browned on both sides.</li>
  <li>Wash and chop the salad.</li>
  <li>Fill pitta with salad, humous, and fried halloumi.</li>
</ol>
```

![](https://origin.chaizz.com/tc/Snipaste_2022-08-19_14-52-03.png)

[`reversed`](https://developer.mozilla.org/zh-CN/docs/Web/HTML/Element/ol#attr-reversed) 属性将启动列表倒计数。

```html
<ol start="4" reversed>
  <li>Toast pitta, leave to cool, then slice down the edge.</li>
  <li>Fry the halloumi in a shallow, non-stick pan, until browned on both sides.</li>
  <li>Wash and chop the salad.</li>
  <li>Fill pitta with salad, humous, and fried halloumi.</li>
</ol>
```

![](https://origin.chaizz.com/tc/Snipaste_2022-08-19_14-53-32.png)

[`value`](https://developer.mozilla.org/zh-CN/docs/Web/HTML/Element/ol#attr-value) 属性允许设置列表项指定数值。

```html
<ol>
  <li value="2">Toast pitta, leave to cool, then slice down the edge.</li>
  <li value="4">Fry the halloumi in a shallow, non-stick pan, until browned on both sides.</li>
  <li value="6">Wash and chop the salad.</li>
  <li value="8">Fill pitta with salad, humous, and fried halloumi.</li>
</ol>
```

![](https://origin.chaizz.com/tc/Snipaste_2022-08-19_14-54-25.png)



## 3 CSS连接样式

善于利用伪类有效地建立链接状态是很重要的，以及如何为链接添加样式来实现常用的功能，比如说导航栏、选项卡。

### 3.1 连接状态

连接处于不同的状态，都处于一个状态都可以利用对应的伪类来应用样式。

没有访问过的状态，连接的默认状态，可以使用`:link`来应用样式。

被访问过的状态（存在与浏览器的历史状态），可以使用`:visited`来应用样式。

鼠标悬停在连接上的状态，可以使用`:hover`伪类来应用样式。

被选中的时状态 (比如通过键盘的 Tab 移动到这个链接的时候，或者使用编程的方法来选中这个链接 `HTMLElement.focus()`) 可以使用` :focus`伪类来应用样式。

一个链接当它被激活的状态 (比如被点击的时候)，它可以使用`:active`伪类来应用样式。

连接的一些默认状态

- 链接具有下划线。
- 未访问过的 (Unvisited) 的链接是蓝色的。
- 访问过的 (Visited) 的链接是紫色的。
- 悬停 (Hover) 在一个链接的时候鼠标的光标会变成一个小手的图标。
- 选中 (Focus) 链接的时候，链接周围会有一个轮廓。
- 激活 (Active) 链接（点击链接时）的时候会变成红色 。



### 3.2 连接中包含图标

常见的做法是在链接中包含图标，使链接提供更多关于链接指向的内容的信息。

```css
a[href*="http"] {
    background: url('icons8-external-link-26.png') no-repeat 100% 0;
    background-size: 16px 16px;
    padding-right: 19px;
}
```



## 4 web字体

在浏览器中使用指定字体。

首先，在 CSS 的开始处有一个[`@font-face`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/@font-face)块，它指定要下载的字体文件：

```css
@font-face {
  font-family: "myFont";
  src: url("myFont.ttf");
}
```

然后应用到文档中去：

```css
html {
  font-family: "myFont", "Bitstream Vera Serif", serif;
}
```

















































