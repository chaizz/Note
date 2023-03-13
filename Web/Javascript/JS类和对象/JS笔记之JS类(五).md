---
title: JS笔记之JS类(五)
author: chaizz
date: 2023-3-13
tags: JavaScript
photo: ["https://tc.chaizz.com/ec55444c4a1211edac740242ac190002.png"]
---

​         

<!--more-->

JS笔记之JS类(五)

类检查 instanceof

instanceof 操作符用于检查一个对象是否属于某个特定的 class。同时，它还考虑了继承。

在许多情况下，可能都需要进行此类检查。例如，它可以被用来构建一个 **多态性（polymorphic）** 的函数，该函数根据参数的类型对参数进行不同的处理。

```js
obj instanceof Class
//如果 obj 隶属于 Class 类（或 Class 类的衍生类），则返回 true。


例如：
class Rabbit {}
let rabbit = new Rabbit();

// rabbit 是 Rabbit class 的对象吗？
console.log( rabbit instanceof Rabbit ); // true
```

还可以与构造函数一起使用:

```js
// 这里是构造函数，而不是 class
function Rabbit() {}

alert( new Rabbit() instanceof Rabbit ); // true
```

与内建的类一起使用：

```js
let arr = [1, 2, 3];
alert( arr instanceof Array ); // true
alert( arr instanceof Object ); // true
```

> 通常，`instanceof` 在检查中会将原型链考虑在内。此外，我们还可以在静态方法 `Symbol.hasInstance` 中设置自定义逻辑。



obj instanceof Class 的 算法逻辑如下：

1. 如果Class 有静态方法：Symbol.hasInstance，那么就直接调用这个方法，例如：
   ```js
   // 设置 instanceOf 检查
   // 并假设具有 canEat 属性的都是 animal
   class Animal {
       static [Symbol.hasInstance](obj) {
           if (obj.canEat) return true;
       }
   }
   
   let obj = { canEat: true };
   
   console.log(obj instanceof Animal); // true：Animal[Symbol.hasInstance](obj) 被调用
   ```

2. 大多数 class 没有 Symbol.hasInstance。在这种情况下，标准的逻辑是：使用 obj instanceOf Class 检查 Class.prototype 是否等于 obj 的原型链中的原型之一。也就是在Class 的原型链上一步一步的比较。

   ```js
   obj.__proto__ === Class.prototype?
   obj.__proto__.__proto__ === Class.prototype?
   obj.__proto__.__proto__.__proto__ === Class.prototype?
   ...
   // 如果任意一个的答案为 true，则返回 true
   // 否则，如果我们已经检查到了原型链的尾端，则返回 false
   
   // 继承中的比较
   class Animal {}
   class Rabbit extends Animal {}
   
   let rabbit = new Rabbit();
   console.log(rabbit instanceof Animal); // true
   
   // rabbit.__proto__ === Animal.prototype（无匹配）
   // rabbit.__proto__.__proto__ === Animal.prototype（匹配！）
   ```





这里还要提到一个方法 [objA.isPrototypeOf(objB)](https://developer.mozilla.org/zh/docs/Web/JavaScript/Reference/Global_Objects/object/isPrototypeOf)，如果 `objA` 处在 `objB` 的原型链中，则返回 `true`。所以，可以将 `obj instanceof Class` 检查改为 `Class.prototype.isPrototypeOf(obj)`。

这很有趣，但是 `Class` 的 constructor 自身是不参与检查的！检查过程只和原型链以及 `Class.prototype` 有关。

创建对象后如果更改对象的 prototype 属性，那么示例就不再是类的示例了。

```js
function Rabbit() { }
let rabbit = new Rabbit();

// 修改了 prototype
Rabbit.prototype = {};

// ...再也不是 rabbit 了！
console.log(rabbit instanceof Rabbit); // false
```



使用 Object.prototype.toString 方法来揭示类型

一个普通的对象被转化为字符串时得到的结果是：[Object Object]，这是通过toString方法实现的，但是此处有一个隐藏功能，可以使toString变得更加强大，可以将其作为 typeof 的增强版或者 instanceof 的替代方法来使用。

按照 规范 所讲，内建的 toString 方法可以被从对象中提取出来，并在任何其他值的上下文中执行。其结果取决于该值。

- 对于 number 类型，结果是 `[object Number]`
- 对于 boolean 类型，结果是 `[object Boolean]`
- 对于 `null`：`[object Null]`
- 对于 `undefined`：`[object Undefined]`
- 对于数组：`[object Array]`
- ……等（可自定义）

例如

```js
// 方便起见，将 toString 方法复制到一个变量中
let objectToString = Object.prototype.toString;

// 它是什么类型的？
let arr = [];

// 在内部，toString 的算法会检查 this，并返回相应的结果
console.log( objectToString.call(arr) ); // [object Array]
console.log( objectToString.call(123) ); // [object Number]
console.log( objectToString.call(null) ); // [object Null]
console.log( objectToString.call(console.log) ); // [object Function]
```



可以使用特殊的对象属性，Symbol.toStringTag 自定义对象的 toString 方法的行为。

```js
let user = {
    [Symbol.toStringTag]: "User"
};

console.log({}.toString.call(user)); // [object User]

// 特定于环境的对象和类的 toStringTag：
alert( window[Symbol.toStringTag]); // Window
alert( XMLHttpRequest.prototype[Symbol.toStringTag] ); // XMLHttpRequest

alert( {}.toString.call(window) ); // [object Window]
alert( {}.toString.call(new XMLHttpRequest()) ); // [object XMLHttpRequest]
```

正如我们所看到的，输出结果恰好是 `Symbol.toStringTag`（如果存在），只不过被包裹进了 `[object ...]` 里。所以，如果我们想要获取内建对象的类型，并希望把该信息以字符串的形式返回，而不只是检查类型的话，我们可以用 `{}.toString.call` 替代 `instanceof`。



类型检查方法：

|               | 用于                                                         | 返回值     |
| :------------ | :----------------------------------------------------------- | :--------- |
| `typeof`      | 原始数据类型                                                 | string     |
| `{}.toString` | 原始数据类型，内建对象，包含 `Symbol.toStringTag` 属性的对象 | string     |
| `instanceof`  | 对象                                                         | true/false |
