---
title: JS笔记之JS对象基础知识(七)
author: chaizz
date: 2023-1-18 14:54:42
tags: JavaScript
photo: ["https://tc.chaizz.com/ec55444c4a1211edac740242ac190002.png"]
---

​          

<!--more-->

# JS笔记之JS对象基础知识(七)

## 1 symbol类型

根据规范，对象的关键字数据类型只有两种，字符串和symbol类型，如果使用数字或者 true会自动转化为字符串类型。

### 1.1 symbol定义

symbol表示唯一的标识符，可以使用Symbol()来创建这种类型的值。

```js
let id = Symbol();
```

创建时，我们可以给 symbol 一个描述（也称为 symbol 名），这在代码调试时非常有用。

```js
// id 是描述为 "id" 的 symbol
let id = Symbol("id");
```

symbol 保证是唯一的。即使我们创建了许多具有相同描述的 symbol，它们的值也是不同。描述只是一个标签，不影响任何东西。

```js
let id1 = Symbol("id");
let id2 = Symbol("id");

alert(id1 == id2); // false
```

### 1.2 使用Symbol

在JS中大多数数值都支持字符串的隐士转换，但Symbol不会被自动转换。

```js
let id1 = Symbol("id");
let id2 = Symbol("id");

alert(id1 == id2); // false
```



### 1.3 显示Symbol 

如果要显示一个Symbol，需要使用toString()，或者获取`symbol.description`。

```js
let id = Symbol("id");
alert(id.toString()); // Symbol(id)，现在它有效了

let id = Symbol("id");
alert(id.description); // id
```



### 1.4 隐藏属性

Symbol 允许我们创建对象的隐藏属性，代码的任何其他的部分都不能访问后者重写这些属性。例如：

```js
let user = { // 属于另一个代码
  name: "John"
};

let id = Symbol("id");

user[id] = 1;

alert( user[id] ); // 我们可以使用 symbol 作为键来访问数据
```

这样写的好处就是：因为`user` 对象属于另一个代码库，所以向它们添加字段是不安全的，因为我们可能会影响代码库中的其他预定义行为。但 symbol 属性不会被意外访问到。第三方代码不会知道新定义的 symbol，因此将 symbol 添加到 `user` 对象是安全的。

### 1.5 对象字面量中的 symbol

在通过字面量创建对象时，需要使用方括号把它括起来。

```js
let id = Symbol("id");

let user = {
  name: "John",
  [id]: 123 // 而不是 "id"：123
};
```



> Tips：Symbol 属性会在for..in 中被跳过。
>
> Object.keys(user) 也会忽略它们。这是一般“隐藏符号属性”原则的一部分。如果另一个脚本或库遍历我们的对象，它不会意外地访问到符号属性。
>
> 但是 **Object.assign** 会同时复制字符串和Symbol属性。



### 1.6 全局Symbol

通常所有的Symbol都是不同的对象，即使他们有相同的名字。但是有时候我们想要相同名字的symbol具有相同的实体，例如应用程序的不同部分想要访问的symbol 指的是完全相同的属性。

在JS中有一个全局的Symbol注册表，可以创建并在全局访问他们，可以确保每次访问相同名字的symbol返回的都是相同的Symbol。



### 1.7 Symbol.for(key)  

key 为Symbol的描述。从注册表读取并创建symbol， 读取时如果不存在自动创建。

```js
// 从全局注册表中读取
let id = Symbol.for("id"); // 如果该 symbol 不存在，则创建它

// 再次读取（可能是在代码中的另一个位置）
let idAgain = Symbol.for("id");

// 相同的 symbol
alert( id === idAgain ); // true
```

注册表内的Symbol被称为全局Symbol，如果我们想要一个应用程序范围内的 symbol，可以在代码中随处访问 ——这就是它们的用途。



### 1.8 Symbol.keyFor(key) 

可以根据Symbol的变量名返回Symbol的描述。

```js
// 通过 name 获取 symbol
let sym = Symbol.for("name");
let sym2 = Symbol.for("id");

// 通过 symbol 获取 name
alert( Symbol.keyFor(sym) ); // name
alert( Symbol.keyFor(sym2) ); // id
```

Symbol.keyFor使用全局注册表来查找symbol的键，所以它不适用于非全局的Symbol，如果Symbol不是全局的他将无法找回并返回Undefined。

```js
let globalSymbol = Symbol.for("name");
let localSymbol = Symbol("name");

alert( Symbol.keyFor(globalSymbol) ); // name，全局 symbol
alert( Symbol.keyFor(localSymbol) ); // undefined，非全局

alert( localSymbol.description ); // name
```

### 1.9 系统Symbol

JS内部有许多的系统Symbol，我们可以用他们来微调对象的各个方面。在[symbol列表](https://tc39.es/ecma262/#sec-well-known-symbols)中找到。



### 1.10 总结

- `symbol` 是唯一标识符的基本类型

- symbol 是使用带有可选描述（name）的 `Symbol()` 调用创建的。

- symbol 总是不同的值，即使它们有相同的名字。如果我们希望同名的 symbol 相等，那么我们应该使用全局注册表：`Symbol.for(key)` 返回（如果需要的话则创建）一个以 `key` 作为名字的全局 symbol。使用 `Symbol.for` 多次调用 `key` 相同的 symbol 时，返回的就是同一个 symbol。

symbol 有两个主要的使用场景：

1. “隐藏” 对象属性。

   如果我们想要向“属于”另一个脚本或者库的对象添加一个属性，我们可以创建一个 symbol 并使用它作为属性的键。symbol 属性不会出现在 `for..in` 中，因此它不会意外地被与其他属性一起处理。并且，它不会被直接访问，因为另一个脚本没有我们的 symbol。因此，该属性将受到保护，防止被意外使用或重写。

   因此我们可以使用 symbol 属性“秘密地”将一些东西隐藏到我们需要的对象中，但其他地方看不到它。

2. JavaScript 使用了许多系统 symbol，这些 symbol 可以作为 `Symbol.*` 访问。我们可以使用它们来改变一些内建行为。例如：使用 `Symbol.iterator` 来进行 [迭代](https://zh.javascript.info/iterable) 操作，使用 `Symbol.toPrimitive` 来设置 [对象原始值的转换](https://zh.javascript.info/object-toprimitive) 等等。

从技术上说，symbol 不是 100% 隐藏的。有一个内建方法 [Object.getOwnPropertySymbols(obj)](https://developer.mozilla.org/zh/docs/Web/JavaScript/Reference/Global_Objects/Object/getOwnPropertySymbols) 允许我们获取所有的 symbol。还有一个名为 [Reflect.ownKeys(obj)](https://developer.mozilla.org/zh/docs/Web/JavaScript/Reference/Global_Objects/Reflect/ownKeys) 的方法可以返回一个对象的 **所有** 键，包括 symbol。但大多数库、内建方法和语法结构都没有使用这些方法。
