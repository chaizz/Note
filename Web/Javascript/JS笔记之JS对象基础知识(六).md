---
title: JS笔记之JS对象基础知识(六)
author: chaizz
date: 2023-1-18 14:01:12
tags: JavaScript
photo: ["https://tc.chaizz.com/ec55444c4a1211edac740242ac190002.png"]
---

​          

<!--more-->

# JS笔记之JS对象基础知识(六)

## 1 可选链 "?."

可选链`?.`是一种访问嵌套对象属性的安全的方式，即使中间的属性不存在，也不会出现错误。

### 1.1 "不存在的属性"的问题

例如在一个嵌套的对象中，我们获取user对象的address对象的street属性，但是实际上user是一个空对象，此时JS会返回错误，这是正常的返回，但是有时候我们需要的不抛出异常，而是返回`undefined`，表示user对象的address对象没有该属性。

```js
let user = {}; // 一个没有 "address" 属性的 user 对象

alert(user.address.street); // Error!
```

又或者是我们获取htmlDOM元素得时候，获取某个元素的 innerHTML 属性，如果获取不到该元素，也会抛出一个异常。

```js
/ 如果 document.querySelector('.elem') 的结果为 null，则这里不存在这个元素
let html = document.querySelector('.elem').innerHTML; // 如果 document.querySelector('.elem') 的结果为 null，则会出现错误
```

### 1.2 如何去避免这一问题呢？

可能比较容易想到的方法就是获取到该对象以后，使用三元运算符进行比较：`user.address ? user.address.street : undefined`，但是`user.address`重复出现，如果对象之间如果嵌套的更深，代码则会有大量重复。

还有一个方法是使用`&&`运算符，但是任然会有 大量的代码重复。使用可选链就可以解决该问题。

### 1.3 可选链语法

如果可选链`?.`前面的值为`undefined`和`null` 它会停止运算，并且返回`ubdefined`。

例如`value?.pop`:

- 如果value存在，则结果与value.pop 属性值相同。
- 如果当value的是undefined和null时，则返回undefined。

比如使用这种方式访问上面的 `user.address.street`

```js
let user = {}; // user 没有 address 属性

console.log( user?.address?.street ); // undefined（不报错）
```

> Tips：`?.`使其前面（左边）的值成为可选值，但不会对后面的值起作用。

> **不要过度使用可选链**
>
> 我们应该只将 `?.` 使用在一些东西可以不存在的地方。
>
> 例如，如果根据我们的代码逻辑，`user` 对象必须存在，但 `address` 是可选的，那么我们应该这样写 `user.address?.street`，而不是这样 `user?.address?.street`。
>
> 那么，如果 `user` 恰巧为 undefined，我们会看到一个编程错误并修复它。否则，如果我们滥用 `?.`，会导致代码中的错误在不应该被消除的地方消除了，这会导致调试更加困难。

> **`?.` 前的变量必须已声明**
>
> 如果未声明变量 `user`，那么 `user?.anything` 会触发一个错误。可选链仅适用于已声明的变量。



### 1.4 可选链的短路效应

即可选链的左边不存在，就会立刻停止运行（短路效应）。因此如果在`?.`的右侧有任何下一步的操作都不会执行。

### 1.5 可选链的变体：?.[] ?.()

可选链不是一个运算符，而是一中特殊的语法结构，他还可以与函数以及方括号一起使用。

例如`?.()`调用一个可能不存在的函数。在下面这段代码中，有些用户具有 `admin` 方法，而有些没有：

```js
let userAdmin = {
  admin() {
    alert("I am admin");
  }
};

let userGuest = {};

userAdmin.admin?.(); // I am admin

userGuest.admin?.(); // 啥都没发生（没有这样的方法）
```

使用方括号获取属性的方式：

```js
let key = "firstName";

let user1 = {
  firstName: "John"
};

let user2 = null;

alert( user1?.[key] ); // John
alert( user2?.[key] ); // undefined
```

此外还可以将`?.`和 `delete`一起使用：

```js
delete user?.name; // 如果 user 存在，则删除 user.name
```



> Tips：需要注意的是可选链可以进行安全的读取和删除，但是不能进行写入。可选链不能用在赋值语句的左边。

```js
let user = null;

user?.name = "John"; // Error，不起作用
// 因为它在计算的是：undefined = "John"
```



### 1.6 总结

可选链的语法格式有大概几种：

- obj?.prop    ---> 如果obj存在则返回`obj.prop`如果不存在则返回`undefined`。
- obj?.[prop]  ---> 如果obj存在则返回`obj.prop`如果不存在则返回`undefined`。、
- obj.method?.()  ---> 如果`obj.method`存在则调用`obj.method()`，否则返回 `undefined`。

我们应该谨慎的使用`?.`，根据我们的代码逻辑，仅在当做部分不存在的可接受的条件下使用最好。且该语法格式是JS新出的特性，可能会存在兼容问题，旧式浏览器可能需要 polyfills。