---
title: js笔记之Promise(一)
author: chaizz
date: 2023-3-22
tags: JavaScript
photo: ["https://tc.chaizz.com/ec55444c4a1211edac740242ac190002.png"]
---

​          

<!--more-->

# js笔记之Promise(一)

## 

Promise 对象的构造器语法

```js
let promise = new Promise(function (resolve, reject) {
    // executor（生产者代码，“歌手”）
});
```



传递给Promise的函数被称为 executor，当 new Promise 被创建时，executor 会自动运行，它包含最终应产生结果的生产者代码。

函数的参数 resolve，reject 是由JS自身提供的回调，我们的代码仅在executor的内部，当executor得到的结果，无论早晚，他应该调用一下的回调之一：

- `resolve(value)` —— 如果任务成功完成并带有结果 `value`。
- `reject(error)` —— 如果出现了 error，`error` 即为 error 对象。

所以总结一下就是executor会自动运行并尝试执行一项工作，尝试结束后，如果成功则调用resolve，如果出现error则调用reject。

由new Promise 构造器返回的promise对象具有以下的属性：

- state 最初是padding 然后再 resolve被调用时，变为fulfilled, 或者在reject被调用时变为rejected。
- result 最初是 undefined 然后在resolve(value)  被调用时变为value，或者在 reject(error) 被调用时变为 error。

所以executor最终将promise移至一下状态之一：

![image-20230322204449440](https://tc.chaizz.com/tc/image-20230322204449440.png)

一个简单的例子：

下面的new Promise 构造器和一个简单的函数，该executor具有包含事件的生产者代码。

```js
let promise = new Promise(function (resolve, reject) {
    // 当 promise 被构造完成时，自动执行此函数

    // 1 秒后发出工作已经被完成的信号，并带有结果 "done"
    setTimeout(() => resolve("done"), 1000);
});



let promise = new Promise(function (resolve, reject) {
    resolve("done");

    reject(new Error("…")); // 被忽略
    setTimeout(() => resolve("…")); // 被忽略
});
```

executor 只能调用一个 `resolve` 或一个 `reject`。任何状态的更改都是最终的。所有其他的再对 resolve 和 reject 的调用都会被忽略：

```js
let promise = new Promise(function (resolve, reject) {
    resolve("done");

    reject(new Error("…")); // 被忽略
    setTimeout(() => resolve("…")); // 被忽略
});
```



