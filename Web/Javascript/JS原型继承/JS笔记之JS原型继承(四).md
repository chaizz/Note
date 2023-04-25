---
title: JS笔记之JS原型继承(四)
author: chaizz
date: 2023-3-06
tags: JavaScript
photo: ["https://origin.chaizz.com/ec55444c4a1211edac740242ac190002.png"]
---

​         

<!--more-->

# JS笔记之JS原型继承(四)

## 1 原型方法，没有`__proto__`的对象

现在使用`obj.__proto__`获取对象的原型，是已经过时的方法，更加现代的方式是:

- Object.getPrototypeOf(obj) 返回对象的[[prototype]]。
- Object.setPrototypeOf(obj, proto)  将对象的[[prototype]]设置为proto。

`__proto__`不被反对的唯一作用就是在创建对象时，指定对象的`__proto__`属性，例如：`{__proto__:...}`。

还有一种特殊的方法：`Object.create(proto, [descriptors])`  利用给定的`proto`作为[[prototype]]和可选的属性描述来创建一个新的对象。例如：

```js
let animal = {
    eats: true
};

// 创建一个以 animal 为原型的新对象
let rabbit = Object.create(animal); // 与 {__proto__: animal} 相同

console.log(JSON.stringify(rabbit.__proto__)); // {"eats":true}
console.log(rabbit.eats); // true
console.log(Object.getPrototypeOf(rabbit) === animal); // true

Object.setPrototypeOf(rabbit, {}); // 将 rabbit 的原型修改为 {}
```

使用Object.create(proto[,descriptors]) 更加强大，因为他有第二个参数：属性描述器，我们可以为新的对象提供额外的属性。例如：

```js
let animal = {
    eats: true
};

let rabbit = Object.create(animal, {
    jumps: {
        value: true
    }
});

console.log(rabbit.jumps); // true
```



我们可以使用这个方法来创建一个新的和原来一模一样的对象，他比for...in实现克隆对象更加强大，他会把对象的属性，数据属性，setter/getter 包括所有的内容，并且带有正确的[[prototype]]。



## 2 原型简史

为什么会有那么多处理对象原型的操作？因为原型继承一开始存储在与语言当中，但是管理他的方式随着时间推移而演变。

- 构造函数的`prototype`的属性，自古以来就起作用，这个是使用给定原型创建对象的最古老的方式。
- 2012年`Object.create`出现，他提供了使用给定原型创建对象的能力，但是没有提供get/set的能力，一些浏览器实现了非标准的`__proto__`的访问器，来提供更多的灵活性。
- 2015年`Object.setPrototypeOf` 和 `Object.getPrototypeOf` 被加入到标准中，执行与 `__proto__` 相同的功能。由于 `__proto__` 实际上已经在所有地方都得到了实现，但它已过时，所以被加入到该标准的附件 B 中，即：在非浏览器环境下，它的支持是可选的。
- 在2022年，官方允许在对象字面量 `{...}` 中使用 `__proto__`（从附录 B 中移出来了），但不能用作 getter/setter `obj.__proto__`（仍在附录 B 中）

>Tips:
>
>从技术上讲，我们可以在任何时候get/set[[prototype]]，但是我们通常只在创建对象的时候设置一次，自那之后就不在修改。
>
>并且JS引擎做了高度优化，用 `Object.setPrototypeOf` 或 `obj.__proto__=` “即时”更改原型是一个非常缓慢的操作，因为它破坏了对象属性访问操作的内部优化。因此，除非你知道自己在做什么，或者 JavaScript 的执行速度对你来说完全不重要，否则请避免使用它。



## 3 为什么要用`Object.setPrototypeOf` 和 `Object.getPrototypeOf`取代`__proto__`呢？

当我们尝试在对象中存储用户提供的键，例如一个用户输入的字典，我们就会发现问题：其他的key都可正常的工作，除了key是`__proto__`时。例如：

```js
let obj = {};

let key = prompt("What's the key?", "__proto__");
obj[key] = "some value";

alert(obj[key]); // [object Object]，并不是 "some value"
```

所以说我们无法决定用户输入的字典key，对于开发人员来说对象的`__proto__`属性只能是对象或者null，但是对于用户来说，可以是任何东西。用户看来这就是一个BUG。

当然我们可以手动的规避这类的问题，比如我们是用Map来代替对象尽心存储，那么这一切都会迎刃而解。

```js
let map = new Map();

let key = prompt("What's the key?", "__proto__");
map.set(key, "some value");

alert(map.get(key)); // "some value"（符合预期）
```

但是对象的语法更加的简介明确，这里我们可以使用一些技巧来实现解决上述的BUG。

我们知道`__proto__`不是对象的属性，而是Object.prototype的访问器属性。因此如果`obj.__proto__`被读取或者被赋值，那么对应的getter/setter会被从他的原型中调用，他会set/get[[prototype]]。具体的操作方法：

```js
// 1. 此处我们创建了一个空对象，他的原型是null，因此他没有继承__proto__的getter/setter方法。
let obj = Object.create(null);
// 或者：obj = { __proto__: null }

let key = prompt("What's the key?", "__proto__");
obj[key] = "some value";

alert(obj[key]); // "some value"
```



我们可以把上述代码的对象叫做"very plain"或者"pure dictionary"对象，因为他们比通常的普通对象还要简单。缺点就是这样的对象没有任何内建的方法，例如：toString。

需要注意的是，大多数关于对象的方法都是使用Object.somethin(...)，例如Object.keys(obj)，他们不在prototype中，因此在 “very plain” 对象中它们还是可以继续使用：

```js
let chineseDictionary = Object.create(null);
chineseDictionary.hello = "你好";
chineseDictionary.bye = "再见";

console.log(Object.keys(chineseDictionary)); // hello,bye
```



## 4 总结：

要使用给定的原型来创建对象：

- 字面量语法：`{__proto__:...}`, 允许指定多个值。
- `Object.create(proto, [descriptors])` 允许指定描述符。

Object.create 提供了一种简单的方式来浅拷贝对象，及其所有的属性描述符。

```js
let clone = Object.create(Object.getPrototypeOf(obj), Object.getOwnPropertyDescriptors(obj));
```



使用 `Object.create(null)` 或 `{__proto__: null}` 创建的无原型的对象。这些对象被用作字典，存储任意键。

通常，对象会从 `Object.prototype` 继承内建的方法和 `__proto__` getter/setter，会占用相应的键，且可能会导致副作用。原型为 `null` 时，对象才真正是空的。
