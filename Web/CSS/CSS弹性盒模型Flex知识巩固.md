---
title: CSS弹性盒模型Flex知识巩固
author: chaizz
date 2023-2-27
tags: CSS
photo: ["https://tc.chaizz.com/760e75941d1511edb5630242ac190002.png"]
---

​               

<!--more-->          



所谓弹性盒模型就是首先要有一个盒子，即弹性盒，在盒子中的所有的元素都是弹性元素，我们可以控制里面的元素的排列组合。

例如盒子中有两个元素水平排列时，他们自动等宽排列。我们同样可以使其（弹性元素）竖直排列。

![image-20230227202922436](https://tc.chaizz.com/tc/image-20230227202922436.png)

当增加了一个盒子，会自动变为三个元素水平等宽排列。

![image-20230227203140920](https://tc.chaizz.com/tc/image-20230227203140920.png)

## 1 声明弹性盒子

例如当前我们有这样一个结构：

```html
	<body>
		<article>
			<div>1</div>
			<div>2</div>
			<div>3</div>
		</article>
	</body>
```

```css
* {
	margin: 0;
	padding: 0;
}

article {
    border: 2px solid rgb(15, 25, 171);
    width: 500px;
    margin: 50px auto;
}

article * {
    width: 100px;
    height: 100px;
    background-color: red;
    margin: 10px;
    font-size: 50px;
}
```



![image-20230227204707086](https://tc.chaizz.com/tc/image-20230227204707086.png)

## 2 弹性元素排列

默认的HTML的文档流排列方法为从上到下， 1,2,3 三个`div`以此排列。

当我们在`article`上添加 `display:flex` 时，此时三个`div`将编程如下的排列方式:

```css
article {
    /* 将article转化为弹性盒 */
	display: flex;
	border: 2px solid rgb(15, 25, 171);
	width: 500px;
	margin: 50px auto;
}
```



![image-20230227205029209](https://tc.chaizz.com/tc/image-20230227205029209.png)

弹性盒中的弹性元素默认为水平排列，当元素的个数发生变化，元素宽度会自适应伸缩。我们仍然可以使用`flex-direction`来改变弹性元素的排列方式。

```css
article {
	display: flex;
    /*
    flex-direction：控制弹性元素的排列方式。
        row：默认的排列方式。水平排列
        row-reverse：水平排列，但是弹性元素进行翻转
        column：竖向方向排列
        column-reverse：竖向排列，但是弹性元素进行翻转
    */
    flex-direction: row;  
	border: 2px solid rgb(15, 25, 171);
	width: 500px;
	margin: 50px auto;
}
```



## 3 弹性元素换行

在上文中， 弹性元素都没有弹性盒子的宽度宽，有时候我们会有很多的弹性元素，默认是自适应伸缩的，但是我们想让他们超过弹性盒子宽度时进行换行要怎么实现呢？

```css
article {
	display: flex;
    flex-direction: row;
    /* 
    flex-wrap：控制元素在溢出的时候是否换行 
        npwrap：不换行
        wrap：换行
        wrap-reverse：反向换行
    */
    flex-wrap: wrap;
	border: 2px solid rgb(15, 25, 171);
	width: 300px;
	margin: 50px auto;
}
```



根据`flex-wrap`的属性和`flex-direction`的属性，我们可以控制元素的水平、列排布以及在元素溢出的时候的换行排布。css针对上述属性提供了一个快捷的属性`flex-flow`。

```css
article {
	display: flex;
    /* 
    flex-flow 可以同时设置排列和换行
    */
    flex-flow: column wrap;
	border: 2px solid rgb(15, 25, 171);
	width: 300px;
    height: 300px;
	margin: 50px auto;
}
```



## 4 弹性盒排列的方式

在弹性元素水平竖直排列时候，会有一个轴的概念。弹性盒主要根据轴来区分弹性元素排列的方向。弹性盒内部会有一个主轴副轴之分，如果是默认排列方式：`flex-flow:row nowarp` 此时水平方向的轴为弹性盒的主轴。当元素换行的时候元素就会根据副轴（即竖直方向的轴）进行排列。

![image-20230227212850381](https://tc.chaizz.com/tc/image-20230227212850381.png)

如果是排列方式为：`flex-flow:column nowarp` 此时竖直方向的轴为弹性盒的主轴。当元素换行的时候元素就会根据副轴（即水平方向的轴）进行排列。

![image-20230227213340600](https://tc.chaizz.com/tc/image-20230227213340600.png)

所以根据上面的图例，可以说明在弹性盒模型中，主轴并不一定是水平方向或者是垂直方向，是要根据弹性盒模型的`flex-direction`属性决定的。



## 5 主轴的排列方式（单行）

使用`justify-content`来控制主轴的排列方式。

```js
article {
	display: flex;
	flex-flow: row wrap;
	/* 
    justify-content：控制弹性元素在主轴的对齐方式
        1、当 flex-direction 为 row 时，主轴为水平方向。
            flex-start：从主轴开始对齐， 默认。从左到右。
            flex-end：从主轴结束对齐，从右到左。
            如果 flex-direction 进行翻转：row-reverse，以上两种对齐方式则也会翻转
        2、当 flex-direction 为 column 时，主轴为竖直方向。
            flex-start：从主轴开始对齐， 默认。从上到下。
            flex-end：从主轴结束对齐，从下到上。
            如果 flex-direction 进行翻转：column-reverse，以上两种对齐方式则也会翻转

        3、根据主轴居中
            center：所有元素在主轴之间进行居中显示
        4、平均分布
            4.1  space-evenly：完全的平局分布
            4.2  space-around：元素左右两侧会有相等的间距。
            4.3  space-between：左右两边的元素完全靠边，中间的元素平均分布
    */
	justify-content: space-around;
	border: 2px solid rgb(15, 25, 171);
	width: 600px;
	height: 600px;
	margin: 50px auto;
}
```



![image-20230227221805554](https://tc.chaizz.com/tc/image-20230227221805554.png)

当主轴为竖直方向也是相同的排列规则。



## 6 副轴（交叉轴）的排列方式（单行）



```css
article {
	display: flex;
	flex-flow: row nowrap;
	justify-content: space-around;
    /* 
    align-items 控制弹性元素在副轴（交叉轴）的对齐方式
        1、当主轴为水平方向时：
            flex-start：从副轴开始对齐，从上到下。
            flex-end：从副轴开始对齐，从下到上。
        2、center
            交叉轴的中心对齐。
        3、stretch
            元素拉伸，前提是元素没有高度，否则会进行覆盖。
    
        当主轴为垂直方向时 与水平方向原理一致。
    */
    align-items: stretch;
	border: 2px solid rgb(15, 25, 171);
	width: 800px;
	height: 300px;
	margin: 50px auto;
}

article * {
	width: 100px;
	/* height: 100px; */
	background-color: red;
	margin: 10px;
	font-size: 50px;
}
```



案例：单行元素在弹性盒模型中水平垂直居中。

```html
	<body>
		<article>
			<div>1</div>
			<div>2</div>
			<div>3</div>
		</article>
	</body>
```

```css
* {
	margin: 0;
	padding: 0;
}

article {
    /* 1、将盒子设置为弹性盒模型。 */
	display: flex;
    /* 2、设置主轴方向上的居中。 */
	justify-content: center;
    /* 3、设置副轴（交叉轴）方向上的居中。 */
    align-items: center;
	border: 2px solid rgb(15, 25, 171);
	width: 500px;
	height: 500px;
	margin: 50px auto;
}

article * {
    text-align: center;
    line-height: 100px;
	width: 100px;
	height: 100px;
	background-color: red;
	margin: 10px;
	font-size: 50px;
}
```



步骤：

1. 将盒子设置为弹性盒模型。
2. 设置主轴方向上的居中：`justify-content: center`。
3. 设置副轴（交叉轴）方向上的居中：` align-items: center`。



## 7 多行元素在交叉轴的排列









 