---
title: js笔记之Promise(二)
author: chaizz
date: 2023-3-25
tags: JavaScript
photo: ["https://origin.chaizz.com/ec55444c4a1211edac740242ac190002.png"]
---

​          

<!--more-->

# js笔记之Promise(二)

## 1 Promise链

Promise链的想法是通过.then处理程序链进行传递result。一个例子是这样的：

```js
new Promise(function (resolve, reject) {
    setTimeout(() => resolve(1), 1000); // (*)
    
}).then(function (result) { // (**)
    console.log(result); // 1
    return result * 2;
    
}).then(function (result) { // (***)
    console.log(result); // 2
    return result * 2;
    
}).then(function (result) {
    console.log(result); // 4
    return result * 2;
});
```



他的运行流程如下：

1. 初始程reomise在一秒后执行resolve。
2. 然后.then处理程序被调用，然后又创建了一个新的promise.
3. 下一个 `then` `(***)` 得到了前一个 `then` 的值，对该值进行处理（*2）并将其传递给下一个处理程序。
4. 以此类推...

这样之所以是可行的，是因为每个对 `.then` 的调用都会返回了一个新的 promise，因此我们可以在其之上调用下一个 `.then`。

当处理程序返回一个值时，它将成为该 promise 的 result，所以将使用它调用下一个 `.then`。

**新手常犯的一个经典错误：从技术上讲，我们也可以将多个 `.then` 添加到一个 promise 上。但这并不是 promise 链（chaining）。**

```js
let promise = new Promise(function (resolve, reject) {
    setTimeout(() => resolve(1), 1000);
});

promise.then(function (result) {
    alert(result); // 1
    return result * 2;
});

promise.then(function (result) {
    alert(result); // 1
    return result * 2;
});

promise.then(function (result) {
    alert(result); // 1
    return result * 2;
});
```



## 2 返回Promise

`.then(handler)` 中所使用的处理程序（handler）可以创建并返回一个 promise。

在这种情况下，其他的处理程序将等待它 settled 后再获得其结果。 

> 确切地说，处理程序返回的不完全是一个 promise，而是返回的被称为 “thenable” 对象 —— 一个具有方法 `.then` 的任意对象。它会被当做一个 promise 来对待。
>
> 这个想法是，第三方库可以实现自己的“promise 兼容（promise-compatible）”对象。它们可以具有扩展的方法集，但也与原生的 promise 兼容，因为它们实现了 `.then` 方法。



如果 `.then`（或 `catch/finally` 都可以）处理程序返回一个 promise，那么链的其余部分将会等待，直到它状态变为 settled。当它被 settled 后，其 result（或 error）将被进一步传递下去。



## 3 总结：

![image-20230325231946264](https://origin.chaizz.com/tc/image-20230325231946264.png)















