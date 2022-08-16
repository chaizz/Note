---
title: CSS层叠与继承
author: chaizz
date: 2022-8-16
tags: CSS
photo: ["https://tc.chaizz.com/760e75941d1511edb5630242ac190002.png"]
---

​          

<!--more-->

# CSS 层叠与继承



> 某些时候项目过程中，会有些样式效果没有生效，很有可能就是创建了应用于两个元素的同一规则。



## 1 层叠

层叠可以理解为覆盖，简单的说就是CSS规则的顺序很重要，当应用两条同级别的规则到同一个元素的时候，最后的一个规则即是生效的规则。

```css
h1 { 
    color: red; 
}
h1 { 
    color: blue; 
}
```

```html
<h1>This is my heading.</h1>
```

## 2 优先级

浏览器根据优先级决定当多个规则有不同选择器对应相同的元素的时候需要应用那个规则，他基本上是一个衡量选择器具体选择哪些区域的尺度：

- 外链CSS地址优先级最低。

- 标签选择器选择页面上该类型的所有元素，他的优先级低一点。
- 类选择器相对于标签选择器会具体一点，他会选择页面上中有特定`class`属性值的元素，所以他的优先级就要高一点。
- ID选择器是针对于网页中唯一的标签属性优先级会更高一点。
- 内联样式优先级最高。

### 2.1 浏览器如何计算优先级

不同的选择器会有不同的分值，把这些分值相加就得到特定选择器的权重，然后再进行匹配。

一个选择器有四个部分的分值相加可以认为是：个十百千 四位数的个位数。

1. 千位：如果是内联样式，该位得一分，没有声明选择器所以得分是1000（内联样式优先级最高）。
2. 百位：ID选择器得一分。
3. 十位：类选择器得一分（包括属性选择器、伪类选择器）。
4. 元素（标签）选择器，伪元素选择器器得一分。

> **备注：** 通用选择器 (`*`)，组合符 (`+`, `>`, `~`, ' ')，和否定伪类 (`:not`) 不会影响优先级。

> **警告：** 在进行计算时不允许进行进位，例如，20 个类选择器仅仅意味着 20 个十位，而不能视为 两个百位，也就是说，无论多少个类选择器的权重叠加，都不会超过一个 ID 选择器。



```css

/* 1. specificity: 1-0-1 */
#outer a {
    background-color: red;
}
        
/* 2. specificity: 2-0-1 */
#outer #inner a {
    background-color: blue;
}

/* 3. specificity: 1-0-4 */
#outer div ul li a {
    color: yellow;
}

/* 4. specificity: 1-1-3 */
#outer div ul .nav a {
    color: white;
}

/* 5. specificity: 0-2-4 */
div div li:nth-child(2) a:hover {
    border: 10px solid black;
}

/* 6. specificity: 0-2-3 */
div li:nth-child(2) a:hover {
    border: 10px dashed black;
}

/* 7. specificity: 0-3-3 */
div div .nav:nth-child(2) a:hover {
    border: 10px double black;
}

a {
    display: inline-block;
    line-height: 40px;
    font-size: 20px;
    text-decoration: none;
    text-align: center;
    width: 200px;
    margin-bottom: 10px;
}

ul {
    padding: 0;
}

li {
    list-style-type: none;
} 
```

```html
<div id="outer" class="container">
    <div id="inner" class="container">
        <ul>
            <li class="nav"><a href="#">One</a></li>
            <li class="nav"><a href="#">Two</a></li>
        </ul>
    </div>
</div>
```



针对上面的代码解释：

- 前面两个选择器都是链接背景颜色的样式 — 第二个赢了使得背景变成蓝色因为它多了一个 ID 选择器：优先级 201 vs. 101。
- 第三四个选择器都是链接文本颜色样式 — 第二个（第四个）赢了使得文本变成白色因为它虽然少一个元素选择器，但是多了一个类选择器，多了 9 分。所以优先级是 113 vs. 104。
- 第 5 到 7 个选择器都是鼠标悬停时链接边框样式。第六个显然输给第五个优先级是 23 vs. 24 — 少了个元素选择器。 第七个，比第五第六都高 — 子选择器数量相同，但是有一个元素选择器变成类选择器。所以最后优先级是 33 vs. 23 和 24。

**注意**

有一个特殊的CSS可以覆盖上面的所有的优先级计算：`!important` 用于修改特定属性的值，能够覆盖普通规则的值。

```css
#winning {
    background-color: red;
    border: 1px solid black;
}
    
.better {
    background-color: gray;
    border: none !important;
}
    
p {
    background-color: blue;
    color: white;
    padding: 5px;
}  
```

```html
<p class="better">This is a paragraph.</p>
<p class="better" id="winning">One selector to rule them all!</p>
```



代码解释：

按照以上代码渲染出的效果应该是：`class="better"`的p标签背景是灰色的，字体颜色为白色，内边距为5px，没有边框。

`id="winning"`的p标签背景色是红色的，字体颜色为白色，内边距为5px。同样没有边框。因为在两个p标签都有类属性：`better` 且`better`的`border`设置了`!important`。尽管优先级比较低，也使用了这个CSS样式。

__覆盖`!important`__的方法就是使用另外一个`!important` 具有相同的优先级且顺序靠后的，或者是优先级跟高。



## 3 总结

相互冲突的声明按照以下顺序适用，后面的覆盖前面的。

1. 用户代理样式表中的声明（浏览器的默认样式，在没有设置其他的样式时使用）。
2. 用户样式表中的常规声明 (由用户设置的自定义样式)。
3. 作者样式表中的常规声明 (这些是我们 web 开发人员设置的样式)。
4. 作者样式表中的`!important`声明。
5. 用户样式表中的`!important` 声明





























