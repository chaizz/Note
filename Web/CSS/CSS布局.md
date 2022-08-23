---
title: CSS布局
author: chaizz
date: 2022-8-22
tags: CSS
photo: ["https://tc.chaizz.com/760e75941d1511edb5630242ac190002.png"]

---

​          

<!--more-->

css的布局技术可以让我们控制他们的相对正常布局流、周边元素、父容器或者主视口/窗口的位置。常见的布局形式有：正常布局流、display属性布局、弹性盒子布局、网格布局、浮动布局、定位布局、表格布局、多列布局。

每种技术都有他们的用途，通过理解各个布局的相关理念，构建理想的布局方案。



一些布局技术会覆盖默认的布局技术；

display：默认的一些属性`inline`、`block`、`inline-block`在正常布局流中的表现形式为对应的盒模型的属性。

在CSS中实现页面的布局的主要方法是设定`display`的值，正常的布局流中所有的属性都有一个`display`的值，例如在段落下面显示另外一个段落，是因为他的`display`的值是`block`吗，如果在段落中添加了一个连接，这个段落并不会换行，是应为连接`a`的`display`的属性值是`inline`。

在讨论布局时，最重要的两个值是`display`:`flex`和`display`:`grid`。



弹性布局：flex

主要解决问题：

- 在父内容里面垂直居中一个块内容。

- 使容器的所有子项占用等量的可用宽度/高度，而不管有多少宽度/高度可用。

- 使多列布局中的所有列采用相同的高度，即使它们包含的内容量不同。

使用方法：在想要进行 flex 布局的父元素上应用`display:flex`，所有的子元素都会按照flex进行布局。

```css
.wrapper {
  display: flex;
}
```

```html
<div class="wrapper">
  <div class="box1">One</div>
  <div class="box2">Two</div>
  <div class="box3">Three</div>
</div>
```

除了可以设置父容器的属性以外，还有很多属性可以设置到flex items上，这些属性可以改变flex items在flex布局中占用宽/高的方式，允许它们通过伸缩来适应可用空间。

例如在所有的flex items上设置`flex:1`可以设置所有的flex items都伸展并填充容器。



flex模型说明

当元素表现为 flex 框时，它们沿着两个轴来布局：

![flex_terms.png](https://tc.chaizz.com/tc/flex_terms.png)

- **主轴**（main axis）是沿着 flex 元素放置的方向延伸的轴（比如页面上的横向的行、纵向的列）。该轴的开始和结束被称为 **main start** 和 **main end**。
- **交叉轴**（cross axis）是垂直于 flex 元素放置方向的轴。该轴的开始和结束被称为 **cross start** 和 **cross end**。
- 设置了 `display: flex` 的父元素，被称之为 **flex 容器（flex container）。**
- 在 flex 容器中表现为柔性的盒子的元素被称之为 **flex 项**（**flex item**）。

在flex布局中提供了==行列==布局的选项，通过`flex-direction`设置，他可以指定主轴的方向，默认行布局值为`row`，按照语言的默认方向排成一排。使用`column` 值将改为列布局，还有`row-reverse` 和 `column-reverse` 值反向排列 flex items。

```css
flex-direction: row;
```

在布局中使用固定宽度或者固定高度，flex items可能会溢出弹性盒子，可以使用`flx-wrap:wrap`来避免这种情况。同时也可以设置每个flex items的最小宽度`flex:200px`，使用了以上属性，溢出的flex items将会移动到下一行。

可以将`flex-direction`和`flex-wrap`缩写为 `flex-flow`。

```css
flex-direction: row;
flex-wrap: wrap;

/* 缩写为 */

flex-flow: row wrap;
```



上文中通过`flex:200px` 来控制flex items的宽度，如何动态的设置flex items 占用空间的比例呢？

```css
flex: 1;
```

这是一个没有单位的比例值，表示每个 flex 项沿主轴的可用空间大小。例如设置为1代表每个flex items占用的空间是相等的，占用的空间是在设置 padding 和 margin 之后剩余的空间。因为这是一个比例值，所以数字的大小所产生的效果是一样的。

也可以指定flex的最小值`flex: 1 2-00px;`， 表示每个flex items先给出200px，然后剩余的空间按照分配的比例共享。



flex的缩写与全写

可以指定最多三个值的缩写属性：(不建议使用全写)

- 第一个即上文中的无比例单位，也可以单独指定全写`flex-grow: 1;`。
- 第二个无单位比例是`flex-shrink`，一般用于溢出容器的flex items，他指定了从每个flex items取出多少溢出量，以阻止它们溢出它们的容器。（高级）
- 第三个是最小值，可以单独指定全写`flex-basis`。



弹性布局水平和垂直对齐

通过`align-items`控制flex items在交叉轴上的位置

- 默认的值是 `stretch`，其会使所有 flex 项沿着交叉轴的方向拉伸以填充父容器。如果父容器在交叉轴方向上没有固定宽度（即高度），则所有 flex 项将变得与最长的 flex 项一样长（即高度保持一致）。
- `center` 值会使这些项保持其原有的高度，但是会在交叉轴居中。
-  `flex-start` 或 `flex-end` 这样使 flex 项在交叉轴的开始或结束处对齐所有的值。

可以在每个单独的flex items设置特殊的布局`align-self: flex-end;`。

通过`justify-content`控制flex items在主轴上的位置。

- 默认值是 `flex-start`，这会使所有 flex 项都位于主轴的开始处。
- 使用 `flex-end` 来让 flex 项到结尾处，与上面的值相反。
- 使用`center` 让 flex 项在主轴居中，但是每个元素之间不会有间隔。
- 使用 `space-around` 让flex 项沿着主轴均匀地分布，在任意一端都会留有一点空间。
- 使用 `space-between`，它和 `space-around` 非常相似，只是它不会在两端留下任何空间。



flex items 排序

弹性盒子可以改变flex items的布局的位置，而不会影响到dom元素的顺序。

```css
button:first-child {
    order: 1;
}
```



- 所有 flex items 的默认 `order` 的值是0。
- order 值大的 flex items 比 order 值小的在显示顺序中更靠后。
- 相同 order 值的 flex 项按源顺序显示。所以假如你有四个元素，其 order 值分别是 2，1，1 和 0，那么它们的显示顺序就分别是第四，第二，第三，和第一。
- 第三个元素显示在第二个后面是因为它们的 order 值一样，且第三个元素在源顺序中排在第二个后面。

也可以给 order 设置负值使它们比值为 0 的元素排得更前面。



网格布局：grid

网格是由一系列水平及垂直的线构成的一种布局模式。根据网格，我们能够将设计元素进行排列，帮助我们设计一系列具有固定位置以及宽度的元素的页面，使我们的网站页面更加统一。

一个网格通常还有许多的列与行，以及行与行、列与列之间的间隙，间隙一般称为沟槽。

![img](https://tc.chaizz.com/tc/grid.png)



与弹性盒子的区别：在定义网格后，网页并不会马上发生变化。因为`display: grid`的声明只创建了一个只有一列的网格，所以你的子项还是会像正常布局流那样从上而下一个接一个的排布



给网格设置列

```css
.container {
    display: grid;
    grid-template-columns: 200px 200px 200px;
}
```

设置列的单位可以是具体的px，或者百分比也可以是fr，他比px和百分比更加的灵活，这个单位表示了一个可用空间的一个比例。

```css
grid-template-columns: 1fr 1fr 1fe;
```

设置了fr，每一列的宽度们可以随着空间的变化而变化。

> **`fr`单位分配的是*可用*空间而非*所有*空间，所以如果某一格包含的内容变多了，那么整个可用空间就会减少，可用空间是不包括那些已经确定被占用的空间的。**



网格间隙

使用 `grid-column-gap`属性来定义列间隙；使用 `grid-row-gap`来定义行间隙；使用 `grid-gap` 可以同时设定两者。

```css
.container {
    display: grid;
    grid-template-columns: 2fr 1fr 1fr;
    grid-gap: 20px;
}
```

间隙距离可以用任何长度单位包括百分比来表示，但不能使用`fr`单位。



重复构建行/列

可以使用`repeat`来重复构建具有某些宽度配置的某些列。

```css
.container {
    display: grid;
    grid-template-columns: repeat(3, 1fr);
    grid-gap: 20px;
}
```



显式网格和隐式网格

显式网格是我们用`grid-template-columns` 或 `grid-template-rows` 属性创建的。而隐式网格则是当有内容被放到网格外时才会生成的。显式网格与隐式网格的关系与弹性盒子的 main 和 cross 轴的关系有些类似。

隐式网格的行列大小默认是auto，大小会根据放入的内容自动调整。也可以使用`grid-auto-rows`和`grid-auto-columns`手动设置隐式网格的大小。

简单来说，隐式网格就是为了放显式网格放不下的元素，浏览器根据已经定义的显式网格自动生成的网格部分。



minmax函数

函数为一个行/列的尺寸设置了取值范围。比如设定为 `minmax(100px, auto)`，那么尺寸就至少为 100 像素，并且如果内容尺寸大于 100 像素则会根据内容自动调整。



 自动使多列填充

```css
.container {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(200px, 1fr));
  grid-auto-rows: minmax(100px, auto);
  grid-gap: 20px;
}
```

使用关键字`auto-fill`来代替重复的次数，使用minmax设置每行/列的最小值，最大值`1fr`。





基于线的元素放置

要把元素放置到网格中，是根据网格的分割线来定义的，第一条线的起始点与文档书写模式相关。在英文中，==第一条列分隔线（即网格边缘线）在网格的最左边==而第一条==行分隔线在网格的最上面==。而对于阿拉伯语，第一条列分隔线在网格的最右边，因为阿拉伯文是从右往左书写的。

我们根据这些分隔线来放置元素，通过以下属性来指定从那条线开始到哪条线结束。

- [`grid-column-start`](https://developer.mozilla.org/en-US/docs/Web/CSS/grid-column-start)
- [`grid-column-end`](https://developer.mozilla.org/en-US/docs/Web/CSS/grid-column-end)
- [`grid-row-start`](https://developer.mozilla.org/en-US/docs/Web/CSS/grid-row-start)
- [`grid-row-end`](https://developer.mozilla.org/en-US/docs/Web/CSS/grid-row-end)

这些属性的值均为分隔线序号，你也可以用以下缩写形式来同时指定开始与结束的线。

- [`grid-column`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/grid-column)
- [`grid-row`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/grid-row)

注意开始与结束的线的序号要使用`/`符号分开。



用[`grid-template-areas`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/grid-template-areas)属性放置元素

- 你需要填满网格的每个格子
- 对于某个横跨多个格子的元素，重复写上那个元素`grid-area`属性定义的区域名字
- 所有名字只能出现在一个连续的区域，不能在不同的位置出现
- 一个连续的区域必须是一个矩形
- 使用`.`符号，让一个格子留空













































浮动布局：例如`float:left`值可以让块级元素相互排成一行。

position：允许精准设置盒子中的盒子的位置，正常布局流中的默认值为`static`，他的值会引起不同元素的布局方式，例如将元素固定到浏览器左上角的。

表格布局：表格的布局方式，可以用在非表格内容上，可以使用`display:table`和相关属性在非表格元素上使用。

多列布局：`multi-column-layout`属性可以让快按照列布局，类似报纸的形式。



































