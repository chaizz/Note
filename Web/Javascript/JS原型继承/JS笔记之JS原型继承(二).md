---
title: JS笔记之JS原型继承(二)
author: chaizz
date: 2023-3-06
tags: JavaScript
photo: ["https://origin.chaizz.com/ec55444c4a1211edac740242ac190002.png"]
---

​         

<!--more-->

# JS笔记之JS原型继承(二)

JS在一开始就有了原型继承，但是在过去只是没有直接对其的访问当时，唯一的方法就是通过构造函数的`prototype`属性。



## 1 构造器创建原型对象

使用`F.prototype`去创建一个对象的常规属性，例如：

```js
let animal = {
    eats: true
};

function Rabbit(name) {
    this.name = name;
}
Rabbit.prototype = animal;

// 设置 Rabbit.prototype = animal 的字面意思是：“当创建了一个 new Rabbit 时，把它的 [[Prototype]] 赋值为 animal”。
let rabbit = new Rabbit("White Rabbit"); //  rabbit.__proto__ == animal
console.log(rabbit.eats); // true
```

`F.prototype`仅在`new F` 时被调用，他为新对象的`[[prototype]]`赋值，如果再创建之后`F.prototype`有了变化（`F.prototype = <another object>`），那么通过 `new F` 创建的新对象也将随之拥有新的对象作为 `[[Prototype]]`，但已经存在的对象将保持旧有的值。



## 2 默认的`F.prototype`构造器属性。

每个函数都有`prototype`属性，即使没有提供他。默认的`prototype`是一个只有`constructor`属性的对象。属性`constructor`指向自身。像这样：

```js
function Rabbit() {}
// 默认：Rabbit.prototype = { constructor: Rabbit }
console.log( Rabbit.prototype.constructor == Rabbit ); // true
```

如果对函数什么都不做，`constructor `属性可以通过`[[Prototype]]`给所`rabbits `使用：

```js
function Rabbit() { }
// 默认：
// Rabbit.prototype = { constructor: Rabbit }

let rabbit = new Rabbit(); // 继承自 {constructor: Rabbit}

console.log(rabbit.constructor == Rabbit); // true (from prototype)
```



当我们有一个对象，但不知道它使用了哪个构造器（例如它来自第三方库），并且我们需要创建另一个类似的对象时，可以使用 `constructor` 属性来创建一个新对象，该对象使用与现有对象相同的构造器，用这种方法就很方便：

```js
function Rabbit(name) {
    this.name = name;
    console.log(name);
}

let rabbit = new Rabbit("White Rabbit");
let rabbit2 = new rabbit.constructor("Black Rabbit");
```



但是 **……JavaScript 自身并不能确保正确的 `"constructor"` 函数值。**我们可以对`prototype`的值，进行任意更改：

```js
function Rabbit() { }
Rabbit.prototype = {
    jumps: true
};

let rabbit = new Rabbit();
console.log(rabbit.constructor === Rabbit); // false
```



所以我们为了确保`constructor`的正确性，我们可以选择添加/删除属性到默认 `"prototype"`，而不是整个将其覆盖。

```js
Rabbit.prototype = {
  jumps: true,
  constructor: Rabbit
};
// 这样的 constructor 也是正确的，因为我们手动添加了它
```



## 3 总结

- `F.prototype`只是一个函数的普通的属性，他是为了`new F` 被调用时为新对象`[[prototype]]`赋值。

- `F.prototype` 的值要么是一个对象，要么就是 `null`：其他值都不起作用。

- `"prototype"` 属性仅当设置在一个构造函数上，并通过 `new` 调用时，才具有这种特殊的影响。在常规对象上，`prototype` 没什么特别的。

  ```js
  let user = {
    name: "John",
    prototype: "Bla-bla" // 这里只是普通的属性
  };
  ```

  

