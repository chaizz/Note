---
title: JS笔记之JS对象基础知识(八)
author: chaizz
date: 2023-1-19 11:24:41
tags: JavaScript
photo: ["https://tc.chaizz.com/ec55444c4a1211edac740242ac190002.png"]
---

​          

<!--more-->

# JS笔记之JS对象基础知识(八)

## 1 对象原始值的转换

当对象相加或者相减时，使用 alert(obj) 打印会输出什么？

JS不允许自定义运算符对对象的处理方式，我们无法实现特殊的对象处理方法来处理加法或者是其他的运算。

在此类运算的情况下，对象会被自动转换为原始值，然后对这些原始值进行运算，并得到运算结果（也是一个原始值，这是一个重要的限制：因为 `obj1 + obj2`（或者其他数学运算）的结果不能是另一个对象！

例如我们无法使用对象来表示向量或者是矩阵，或者是成就或者其他。

因此，由于我们从技术上无法实现此类运算，所以在实际项目中不存在对对象的数学运算。如果你发现有，除了极少数例外，通常是写错了。

对象如何转换为原始值，以及如何对其进行自定义。

### 1.1 转换规则

- 没有转换为布尔值，所有的对象在布尔值上下文中均为`true`，只有字符串和数字转换。
- 数字转换发生在对象相减或应用数学函数时，例如Date对象可以相减，date1-date2 的结果是两个日期之间的差值。
- 对于字符串转换，通常发生在我们像alert(obj)这样输出一个对象和类似的上下文中。

我们可以使用特殊的对象方法，实现字符串和数字的转换。

### 1.2 hint定义

类型转换在各种情况下有三种变体，他们被称为hint.

#### 1.2.1 String

对象到字符串的转换，当我们对期望一个字符串的对象执行操作时，如 “alert”：

```js
// 输出
alert(obj);

// 将对象作为属性键
anotherObj[obj] = 123;
```



#### 1.2.2 Number

对象到数字的转换

```js
// 显式转换
let num = Number(obj);

// 数学运算（除了二元加法）
let n = +obj; // 一元加法
let delta = date1 - date2;

// 小于/大于的比较
let greater = user1 > user2;
```

大多数内建的数学函数也包括这种转换。

#### 1.2.3 default

当运算符 "不确定" 期望值的类型时。在极少数情况下发生。

例如二元加法 `+` 可用于字符串的拼接，也可用于数字的相加，因此当二元加法得到对象类型的参数时，他将依据 `default` hint 来对其进行转换。

此外如果对象被用于与字符串、数字、或者symbol进行`==`比较，这时候他进行那种类型转换也不熟很明确，因此使用 `default` hint。

```js
// 二元加法使用默认 hint
let total = obj1 + obj2;

// obj == number 使用默认 hint
if (user == 1) { ... };
```

### 1.3 转换方法

**为了进行转换，JavaScript 尝试查找并调用三个对象方法：**

1. 调用 `obj[Symbol.toPrimitive](hint)` —— 带有 symbol 键 `Symbol.toPrimitive`（系统 symbol）的方法，如果这个方法存在的话，
2. 否则，如果 hint 是 `"string"` —— 尝试调用 `obj.toString()` 或 `obj.valueOf()`，无论哪个存在。
3. 否则，如果 hint 是 `"number"` 或 `"default"` —— 尝试调用 `obj.valueOf()` 或 `obj.toString()`，无论哪个存在。

#### 1.3.1 Symbol.toPrimitive

他是被用来给转换方法命名。如果它存在则他会被用于所有的hint。

```js
obj[Symbol.toPrimitive] = function(hint) {
  // 这里是将此对象转换为原始值的代码
  // 它必须返回一个原始值
  // hint = "string"、"number" 或 "default" 中的一个
}
```



#### 1.3.2 toString/valueOf

如果没有 `Symbol.toPrimitive`，那么 JavaScript 将尝试寻找 `toString` 和 `valueOf` 方法：

- 对于 `"string"` hint：调用 `toString` 方法，如果它不存在，则调用 `valueOf` 方法（因此，对于字符串转换，优先调用 `toString`）。
- 对于其他 hint：调用 `valueOf` 方法，如果它不存在，则调用 `toString` 方法（因此，对于数学运算，优先调用 `valueOf` 方法）。

这些方法必须返回一个原始值。如果 `toString` 或 `valueOf` 返回了一个对象，那么返回值会被忽略（和这里没有方法的时候相同）。

默认情况下，普通对象具有 `toString` 和 `valueOf` 方法：

- `toString` 方法返回一个字符串 `"[object Object]"`。
- `valueOf` 方法返回对象自身。

```js
let user = {name: "John"};

alert(user); // [object Object]
alert(user.valueOf() === user); // true
```



所以如果我们将一个对象当做字符串来使用，例如 alerty(obj) 将会得到`[object Object]`。

### 1.4 自定义转化方法

```js
let user = {
  name: "John",
  money: 1000,

  // 对于 hint="string"
  toString() {
    return `{name: "${this.name}"}`;
  },

  // 对于 hint="number" 或 "default"
  valueOf() {
    return this.money;
  }

};

alert(user); // toString -> {name: "John"}
alert(+user); // valueOf -> 1000
alert(user + 500); // valueOf -> 1500
```



通常我们希望一个全能的地方来处理所有的原始转换。可以只实现toString 来实现。例如：

```js
let user = {
  name: "John",

  toString() {
    return this.name;
  }
};

alert(user); // toString -> John
alert(user + 500); // toString -> John500
```

如果没有 `Symbol.toPrimitive` 和 `valueOf`，`toString` 将处理所有原始转换。



### 1.5 转换可以返回任何原始类型

关于所有原始转换方法，有一个重要的点需要知道，就是它们不一定会返回 “hint” 的原始值。

没有限制 `toString()` 是否返回字符串，或 `Symbol.toPrimitive` 方法是否为 `"number"` hint 返回数字。

唯一强制性的事情是：这些方法必须返回一个原始值，而不是对象。

> Tips：由于历史原因，如果 `toString` 或 `valueOf` 返回一个对象，则不会出现 error，但是这种值会被忽略（就像这种方法根本不存在）。这是因为在 JavaScript 语言发展初期，没有很好的 “error” 的概念。
>
> 相反，`Symbol.toPrimitive` 更严格，它 **必须** 返回一个原始值，否则就会出现 error。



### 1.6 进一步的转换

许多运算符和函数执行类型转换， 例如乘法，将操作数转化为数字。

如果我们将对象作为参数传递，则会出现两个运算阶段：

1. 对象被转化为原始值。根据上面转化的规则。
2. 如果还需进一步计算。则生成的原始值会进一步的转换。

```js
let obj = {
  // toString 在没有其他方法的情况下处理所有转换
  toString() {
    return "2";
  }
};

alert(obj * 2); // 4，对象被转换为原始值字符串 "2"，之后它被乘法转换为数字 2。
```

比如上面的例字：

1. 乘法 `obj * 2` 首先将对象转换为原始值（字符串 “2”）。
2. 之后 `"2" * 2` 变为 `2 * 2`（字符串被转换为数字）。



### 1.7 总结

对象到原始值的转换，是自动触发的。

有三种类型的hint：

- "string" 对于 alert 和其他需要字符串的操作。
- "number" 对于数学运算。
- "default" 少数运算符，通常对象已和 "number" 相同的方式实现 default 转换。

规范明确描述了哪个运算符使用哪个 hint。

转换算法是：

1. 调用 `obj[Symbol.toPrimitive](hint)` 如果这个方法存在，
2. 否则，如果 hint 是 "string"
   - 尝试调用 `obj.toString()` 或 `obj.valueOf()`，无论哪个存在。
3. 否则，如果 hint 是 "number" 或者 "default"
   - 尝试调用 `obj.valueOf()` 或 `obj.toString()`，无论哪个存在。

所有这些方法都必须返回一个原始值才能工作（如果已定义）。

**在实际使用中，通常只实现 `obj.toString()` 作为字符串转换的“全能”方法就足够了，该方法应该返回对象的“人类可读”表示，用于日志记录或调试。**