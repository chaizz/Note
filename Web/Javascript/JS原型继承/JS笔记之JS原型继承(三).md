---
title: JS笔记之JS原型继承(三)
author: chaizz
date: 2023-3-06
tags: JavaScript
photo: ["https://origin.chaizz.com/ec55444c4a1211edac740242ac190002.png"]
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

![image-20230306230056903](https://origin.chaizz.com/tc/image-20230306230056903.png)

当 new Object() 被调用(或者是一个字面量被创建 ={})，这个对象的`[[prototype]]`属性被设置为`Object.prototype`。

![image-20230306230648166](https://origin.chaizz.com/tc/image-20230306230648166.png)

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

![image-20230306232008728](https://origin.chaizz.com/tc/image-20230306232008728.png)



有些对象的方法可能会重叠，比如array的toString方法，和Object的toString方法，当对象调用的时候会根据就近原则进行调用。



## 3 基本数据类型

JS的基本数据类型string，number，boolean，当我们访问他们属性，那么临时包装器对象会通过内建的构造器：String，Number, Boolean被创建，他们提供给我们操作字符串、数字、布尔值的方法然后在消失。

> null 和 undefined 对象没有包装器对象，他们没有方法和属性也没有相应的原型。

## 4 更改原生的原型

原生的原型是可以被修改的，例如我们向String.prototype中添加一个方法，这个方法对所有的字符串都生效。

```js
String.prototype.show = function () {
    alert(this);
};

"BOOM!".show(); // BOOM!
```

但是在开发过程中这样做是不太好的行为，因为原型是全局的，很容易造成冲突。会覆盖到其他的相同的方法。

但是在现代编程中，只有一种情况袁旭修改原型，那就是`polyfilling`，它是一个术语，表示某个方法已经存在，但是特定的JS引擎，不支持该方法，那么我们可以手动实现它，并用以填充内建原型。例如：

```js
if (!String.prototype.repeat) { // 如果这儿没有这个方法
    // 那就在 prototype 中添加它

    String.prototype.repeat = function (n) {
        // 重复传入的字符串 n 次

        // 实际上，实现代码比这个要复杂一些（完整的方法可以在规范中找到）
        // 但即使是不够完美的 polyfill 也常常被认为是足够好的
        return new Array(n + 1).join(this);
    };
}

console.log("La".repeat(3)); // LaLaLa
```



## 5 从原型中借用

在装饰器和转发中，有call和apply的方法，我们可以从一一个对象获取一个方法，并将其赋值给另一个对象。

一些原生类型的方法通常会被借用。例如：

```js
let obj = {
    0: "Hello",
    1: "world!",
    length: 2,
};

obj.join = Array.prototype.join;

console.log(obj.join(',')); // Hello,world!
```

此处，对象obj借用了数组对象的join方法，或者是通过obj.__proto__ 设置为Array.prototype，这样Array的所有方法都可以在obj上直接使用。

但是如果obj从另一个对象中进行了继承，这个方法就不行了，因为会覆盖掉原有的继承。但是此处obj已经从Object进行了继承，又因为Array也继承自Object，所以此处借用的join方法不会影响obj对原有继承的继承，因为obj通过原型链依旧继承了Object。

借用方法很灵活，他允许在需要时混合来自不同的对象的方法。

## 6 总结

所有的内建对象都遵循相同的模式：

- 方法都存储在prototype中。
- 对象本身只存储数据。

原始的数据类型也将相应的方法存储在包装器的peototype中， 只有null和undefined没有包装器对象。

内建的原型可以被修改或者用新的方法填充，但是不建议更改他们，唯一的情况是JS引擎不支持这个方法， 但是已经被加入规范的新的标准。









