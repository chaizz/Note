---
title: 
author: chaizz
date: 2023-3-06
tags: JavaScript
photo: ["https://tc.chaizz.com/ec55444c4a1211edac740242ac190002.png"]
---

​         

<!--more-->

# JS笔记之JS原型继承(三)

`prototype`属性在JS的自身的核心部分，被广泛的应用，所有的内建函数都用到了他。

## 1 原生的原型

首先我们看看原生原型的详细信息，例如我们输出一个空对象：

```js
let obj = {};
alert( obj ); // "[object Object]" ?
```

这个对象弹出的结果是[object Object]，name为什么会输出这样的信息，那些代码输出了这些信息。其实就是一个内建的toString方法，但是他在哪里呢?

在我们学习创建对象时 有两种方法：使用字面量`= {}` 和 使用构造器 `new Object()`，他们两个是一个意思，`Object`就是一个内建的对象构造函数。其自身的`prototype`指向一个带有toString和其他方法的一个巨大对象。

![image-20230306230056903](https://tc.chaizz.com/tc/image-20230306230056903.png)

当 new Object() 被调用(或者是一个字面量被创建 ={})，这个对象的`[[prototype]]`属性被设置为`Object.prototype`。

![image-20230306230648166](https://tc.chaizz.com/tc/image-20230306230648166.png)

所以之后当 `obj.toString()` 被调用时，这个方法是从`object.prototype`中获取的。我们可以验证一下：

```js
let obj = {};

console.log(obj.__proto__ === Object.prototype); // true
console.log(obj.toString === obj.__proto__.toString); //true
console.log(obj.toString === Object.prototype.toString); //true
```



## 2 其他内建原型

其他的对象Array、Date、Function及其他，都早`prototype`上挂在了方法。

例如，当我们创建一个数组，[1, 2, 3]，内部会默认使用new Array() 构造器，因此`Array。prototype`变成了这个数组对象的`protrotype`。并为这个数组对象提供了数组的操作方法，这个的内存的存储效率是很高的。

按照规范所有的内建原型的顶端都是`Object.prototype`，这就是==一切都从对象继承而来==。三个内建对象的示意图：

![image-20230306232008728](https://tc.chaizz.com/tc/image-20230306232008728.png)



有些对象的方法可能会重叠，比如array的toString方法，和Object的toString方法，当对象调用的时候会根据就近原则进行调用。



















