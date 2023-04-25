---
title: JS笔记之JS函数进阶(三)
author: chaizz
date: 2023-2-20
tags: JavaScript
photo: ["https://origin.chaizz.com/ec55444c4a1211edac740242ac190002.png"]
---

<!--more-->

# JS笔记之JS函数进阶(三)

在JS中 函数也是一种值，函数的类型就是Object，函数本身也包含自己的属性。

## 1 name 属性

在创建一个函数时，我们可以根据函数名获取对应函数的name属性，直接创建一个具名函数或者使用命名表达式，来创建函数，函数名和命名表达式的变量名就是函数的name属性。

```js
function sayHi() {
    console.log("Hi");
}
console.log(sayHi.name); // sayHi

let sayHi = function () {
    console.log("Hi");
};
console.log(sayHi.name); // sayHi（有名字！）
```

在对象中的函数同样也是具有该属性，当无法获取该属性时， name的值为空字符串。

```js
// 函数是在数组中创建的
let arr = [function() {}];
console.log( arr[0].name ); // <空字符串>
// 引擎无法设置正确的名字，所以没有值
```



## 2 length 属性

该属性返回的是函数入参的个数，但是不包含...rest的参数，属性 length 有时在操作其它函数的函数中用于做 内省/运行时检查（introspection）。

```js
function f1(a) {}
function f2(a, b) {}
function many(a, b, ...more) {}

alert(f1.length); // 1
alert(f2.length); // 2
alert(many.length); // 2
```



## 3 自定义属性

在函数内部还可以直接自定义属性，直接使用函数名.属性名， 这里的属性名和函数内部的变量名，是两个概念，他们之间没有关系。

```js
function sayHi() {
    console.log("Hi");
  
    // 计算调用次数
    sayHi.counter++;
  }
  sayHi.counter = 0; // 初始值
  
  sayHi(); // Hi
  sayHi(); // Hi
  
  console.log( `Called ${sayHi.counter} times` ); // Called 2 times
```



函数的属性有时候会代替闭包，因为函数属性，可以在函数作用域内，保存局部的变量信息。



## 4 函数命名表达式

带有函数 (NFE，Named Function Expression) 指带有名字的表达式的术语。

一个普通的函数

```js
let funcName = function (params) {
    console.log(params);
}
```

给这个函数加一个名字

```js
let funcName = function func(params) {
    console.log(params);
}
```

此时加名字和不加名字的区别就是：

- 加名字可以在函数的内部调用，
- 在函数的外部是不可见的。

```js
et sayHi = function func(who) {
  if (who) {
    alert(`Hello, ${who}`);
  } else {
    func("Guest"); // 使用 func 再次调用函数自身
  }
};

sayHi(); // Hello, Guest

// 但这不工作：
func(); // Error, func is not defined（在函数外不可见）
```



## 5 使用 new Function 来创建函数

语法

```js
// 该函数是通过给定的 arg1 arg2 参数 和 functionBody 创建的
let func = new Function ([arg1, arg2, ...argN], functionBody);
```

实际的例字

```js
// 包含参数和函数体
let sum = new Function('a', 'b', 'return a + b');
alert( sum(1, 2) ); // 3
// 只有函数体
let sayHi = new Function('alert("Hello")');
sayHi(); // Hello
```

使用的场景：比如我们需要动态的从服务器获取一段代码来运行。



## 6 闭包

闭包是指使用一个特殊的属性，来记录函数自身得创建时的环境的函数，他具体执行了函数创建时的此法环境，但是我们使用 new Function 创建的函数，他并不指向当前的词法环境，而是全局环境。

new Function 这种特性在实际中非常使用，想象一下我们必须通过一个字符串来创建一个函数。在编写脚本时我们不会知道该函数的代码（这也就是为什么我们不用常规方法创建函数），但在执行过程中会知道了。我们可能会从服务器或其他来源获取它。



## 7 总结

- new Function 语法：`let func = new Function ([arg1, arg2, ...argN], functionBody);`

- 使用 new Function 创建的函数，的词法环境执行全局的环境。