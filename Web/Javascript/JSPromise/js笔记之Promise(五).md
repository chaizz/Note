---
title: js笔记之Promise(五)
author: chaizz
date: 2023-3-27
tags: JavaScript
photo: ["https://tc.chaizz.com/ec55444c4a1211edac740242ac190002.png"]
---

​          

<!--more-->

# js笔记之Promise(五)



## 1 微任务

promise 的处理程序 `.then`、`.catch` 和 `.finally` 都是异步的。

即便一个 promise 立即被 resolve，`.then`、`.catch` 和 `.finally` **下面** 的代码也会在这些处理程序之前被执行。

```js
let promise = Promise.resolve();

promise.then(() => console.log("promise done!"));

console.log("code finished"); // 这个 console.log 先显示
```



异步任务需要适当的管理，为此ECMA规定了一个内部队列，PromiseJobs，通常被称为“微任务队列（microtask queue）”（V8 术语）。

如 [规范](https://tc39.github.io/ecma262/#sec-jobs-and-job-queues) 中所述：

- 队列（queue）是先进先出的：首先进入队列的任务会首先运行。
- 只有在 JavaScript 引擎中没有其它任务在运行时，才开始执行任务队列中的任务。

简单的说就是当一个promise 准备就绪时，它的 `.then/catch/finally` 处理程序就会被放入队列中：但是它们不会立即被执行。当 JavaScript 引擎执行完当前的代码，它会从队列中获取任务并执行它。

如果有一个包含多个 `.then/catch/finally` 的链，那么它们中的每一个都是异步执行的。也就是说，它会首先进入队列，然后在当前代码执行完成并且先前排队的处理程序都完成时才会被执行。

**如果执行顺序对我们很重要该怎么办？我们怎么才能让 `code finished` 在 `promise done` 之后出现呢？**

```js
Promise.resolve()
  .then(() => alert("promise done!"))
  .then(() => alert("code finished"));
```



## 2 未处理的 rejection

在js笔记之Promise(三)中说明了，如果一个 promise 的 error 未被在微任务队列的末尾进行处理，则会出现“未处理的 rejection”。现在我们知道`.then/catch/finally` 处理程序就会被放入队列中,

任务队列清空后，avaScript 引擎会触发下面这事件：

```js
let promise = Promise.reject(new Error("Promise Failed!"));

// Promise Failed!
window.addEventListener('unhandledrejection', event => alert(event.reason));
```

当微任务队列中的任务都完成时，才会生成 `unhandledrejection`：引擎会检查 promise，如果 promise 中的任意一个出现 “rejected” 状态，`unhandledrejection` 事件就会被触发。



## 3 总结：

Promise 处理始终是异步的，因为所有 promise 行为都会通过内部的 “promise jobs” 队列，也被称为“微任务队列”（V8 术语）。

因此，`.then/catch/finally` 处理程序总是在当前代码完成后才会被调用。

如果我们需要确保一段代码在 `.then/catch/finally` 之后被执行，我们可以将它添加到链式调用的 `.then` 中。





