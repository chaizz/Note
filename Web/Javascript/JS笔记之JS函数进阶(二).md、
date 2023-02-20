---
title: JS笔记之JS函数进阶(一)
author: chaizz
date: 2023-2-15
tags: JavaScript
photo: ["https://tc.chaizz.com/ec55444c4a1211edac740242ac190002.png"]
---

<!--more-->

# JS笔记之JS函数进阶(一)



## 1 let 和 var 的区别

let 属于现代JS的声明变量的方式，而var则是老旧的方式。所以现在尽可能使用let。

一般情况下可以使用 let 代替var，或者使用var 代替 let，但是在某些方面存在着巨大的差异。

### 1.1 var没有块级作用域

例如：在 if 或者 while 中使用 var 声明的变量，可以在其他的地方获取到。是因为早期的JS中，块没有词法环境。

```js
if (true) {
    var test = true; // 使用 "var" 而不是 "let"
}
console.log(test); // true，变量在 if 结束后仍存在

if (true) {
    let test = true; // 使用 "let"
}
console.log(test); // ReferenceError: test is not defined
```



### 1.2 var 允许重新声明

使用var可以多次声明同一个变量，最新声明的变量就会无效，因为上面已经声明过了。

使用let在同一个作用域下声明两次会提示已经被声明的错误，var 则不会。

```js
let user;
let user; // SyntaxError: 'user' has already been declared

var user = "Pete";
var user = "John"; // 这个 "var" 无效（因为变量已经声明过了）
// ……不会触发错误
console.log(user); // John
```



### 1.3 var 声明的变量 可以再声明之前使用。

这种情况叫做提升，相当于使用var声明的变量 会被提升到一个函数或者脚本的最上面。例如：

```js
function sayHi() {
    console.log(phrase);
    var phrase = "Hello";
}

// 上面的函数就相当于
function sayHi() {
    var phrase
    console.log(phrase);
    phrase = "Hello";
}

sayHi();  // undefined
```

但是有一点值的注意，虽然声明会提升到函数的开始，但是赋值并不会被提升，所以输出的结果就是一个未被赋值的变量，即：undefined。

### 1.4 IIFE

在很久以前，JS中只有var这一种声明变量的方式，且没有快捷作用域，所以程序员就发明了一种模仿块级作用域的方法，这种方法叫做立即调用函数表达式（immediately-invoked function expressions，IIFE）例如：

```js
(function name(params) {
    console.log('立即调用函数表达式');
})()
```

> 现在我们已经没有理由需要这样的方法了。

### 1.5 全局对象

全局对象提供可在任何地方调用的变量和函数。在浏览器中他的名字叫做 window 在nodejs中他的名字叫做global。不同的环境可能会有不同的名字， 所以JS 提出了一个标准名称叫做 globalThis。

使用var 定义的变量，会成为全局对象的属性，函数声明也是同样的，但是不建议直接使用`window.xx` 调用。

> 一般不建议使用全局变量，全局变量应该尽可能的少的使用。



### 1.6 使用 polyfills

有时候我们可以使用全局对象来做兼容性的工作，例如查看全局对象中是否含有 Promise对象，如果没有的话我们可以创建 polyfills，来添加环境中不支持但在现代浏览器标准中存在的对象。

```js
if (!window.Promise) {
    console.log("当前正使用旧的浏览器!");
    // 定制实现现代语言功能
    window.Promise = ...
}
```



## 2 总结

var 和 let 的主要的两个区别：

- var 生明没有块级作用域，仅在当前函数内部可见，或者全局可见。
- var 变量的声明在函数开头就会被处理。

在现代JS中尽可能的使用 const 或者 let。