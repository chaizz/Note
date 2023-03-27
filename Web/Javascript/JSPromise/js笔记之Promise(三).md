---
title: js笔记之Promise(三)
author: chaizz
date: 2023-3-27
tags: JavaScript
photo: ["https://tc.chaizz.com/ec55444c4a1211edac740242ac190002.png"]
---

​          

<!--more-->

# js笔记之Promise(三)



## 1 使用 promise 进行错误处理

promise链在错误中处理中非常强大，当一个 promise 被 reject 时，控制权将移交至最近的 rejection 处理程序。这在实际开发中非常方便。例如：

```js
fetch('https://no-such-server.blabla') // reject
    .then(response => response.json())
    .catch(err => console.log(err)) // TypeError: Failed to fetch（这里的文字可能有所不同）
```



## 2 隐式的try...catch

在 executor 周围的“隐式 `try..catch`”自动捕获了 error，并将其变为 rejected promise。

```js
new Promise((resolve, reject) => {
    resolve("ok");
}).then((result) => {
    throw new Error("Whoops!"); // reject 这个 promise
}).catch(alert); // Error: Whoops!
```



## 2 再次抛出（Rethrowing）

在平常的try...catch中，在catch中遇到我们无法处理的错误我们可以再次的抛出异常，对于promise来 说也是可以的。

如果在promise的.catch中使用throw， 那么控制权就会移交到下一个error的处理程序，如果正常处理error并且正常完成，那么就会到最近的then处理程序。下面是一个例子:

```js
// 执行流：catch -> then
new Promise((resolve, reject) => {

    throw new Error("Whoops!");

}).catch(function (error) {

    console.log("The error is handled, continue normally");

}).then(() => console.log("Next successful handler runs"));
```



## 3 未处理的 rejection

如果一个error没有被处理，会发生什么？此时程序就会像正常开发中抛出了一个错误一样，脚本停止，然后生成一个全局的异常。

如果出现了一个 error，并且在这没有 .catch，那么 unhandledrejection 处理程序就会被触发，并获取具有 error 相关信息的 event 对象，所以我们就能做一些后续处理了。

通常此类 error 是无法恢复的，所以我们最好的解决方案是将问题告知用户，并且可以将事件报告给服务器。

在 Node.js 等非浏览器环境中，有其他用于跟踪未处理的 error 的方法。



## 4 Promise.all

并行执行多个promise, 并等待所有的promise都准备就绪，例如并行下载几个url。

语法：`let promise = Promise.all(iterable);`，他接受一个可迭代对象，通常是一个项为promise的数组。并返回一个新的promise，当所有的promise 都 resolve时，新的promise才会resolve。并且其结果数组就是将成为新的promise的结果。

注意：结果数组中元素的顺序和在源promise中的顺序相同，不管每个promise执行时间的长短。

一个常见的技巧是：讲一个任务数组映射到一个promise, 然后将其包裹到promise.all。例如：

```js
let urls = [
    'https://api.github.com/users/iliakan',
    'https://api.github.com/users/remy',
    'https://api.github.com/users/jeresig'
];

// 将每个 url 映射（map）到 fetch 的 promise 中
let requests = urls.map(url => fetch(url));

// Promise.all 等待所有任务都 resolved
Promise.all(requests)
    .then(responses => responses.forEach(
        response => alert(`${response.url}: ${response.status}`)
    ));

```

又或者另外一个例子：

```js

let names = ['iliakan', 'remy', 'jeresig'];

let requests = names.map(name => fetch(`https://api.github.com/users/${name}`));

Promise.all(requests)
    .then(responses => {
        // 所有响应都被成功 resolved
        for (let response of responses) {
            console.log(`${response.url}: ${response.status}`); // 对应每个 url 都显示 200
        }

        return responses;
    })
    // 将响应数组映射（map）到 response.json() 数组中以读取它们的内容
    .then(responses => Promise.all(responses.map(r => r.json())))
    // 所有 JSON 结果都被解析："users" 是它们的数组
    .then(users => users.forEach(user => console.log(user.name)));
```

> **如果任意一个 promise 被 reject，由 `Promise.all` 返回的 promise 就会立即 reject，并且带有的就是这个 error。**
>
> 且其他的promise也会被忽略，它们的结果也被忽略。



## 5 Promise.allSettled

此API等待所有的 promise 都被 settle，无论结果如何，结果数组都具有一下：

- `{status:"fulfilled", value:result}` 对于成功的响应，
- `{status:"rejected", reason:error}` 对于 error。

一个例子：我们想要获取（fetch）多个用户的信息。即使其中一个请求失败，我们仍然对其他的感兴趣。

```js
let urls = [
    'https://api.github.com/users/iliakan',
    'https://api.github.com/users/remy',
    'https://no-such-url'
];

Promise.allSettled(urls.map(url => fetch(url)))
    .then(results => { // (*)
        results.forEach((result, num) => {
            if (result.status == "fulfilled") {
                alert(`${urls[num]}: ${result.value.status}`);
            }
            if (result.status == "rejected") {
                alert(`${urls[num]}: ${result.reason}`);
            }
        });
    });
```

对于以上的每个 promise，我们都得到了其状态（status）和 `value/reason`。



## 6 Promise.race

与 `Promise.all` 类似，但只等待第一个 settled 的 promise 并获取其结果（或 error）。

```js
Promise.race([
    new Promise((resolve, reject) => setTimeout(() => resolve(1), 1000)),
    new Promise((resolve, reject) => setTimeout(() => reject(new Error("Whoops!")), 2000)),
    new Promise((resolve, reject) => setTimeout(() => resolve(3), 3000))
]).then(alert); // 1
```

在这里第一个 promise 最快，所以它变成了结果。第一个 settled 的 promise “赢得了比赛”之后，所有进一步的 result/error 都会被忽略。



## 7 Pomise.any

与 Promise.race 类似，区别在于 Promise.any 只等待第一个 fulfilled 的 promise，并将这个 fulfilled 的 promise 返回。如果给出的 promise 都 rejected，那么返回的 promise 会带有 AggregateError —— 一个特殊的 error 对象，在其 errors 属性中存储着所有 promise error。

例如：

```js
Promise.any([
    new Promise((resolve, reject) => setTimeout(() => reject(new Error("Whoops!")), 1000)),
    new Promise((resolve, reject) => setTimeout(() => resolve(1), 2000)),
    new Promise((resolve, reject) => setTimeout(() => resolve(3), 3000))
]).then(alert); // 1

```

这里的第一个 promise 是最快的，但 rejected 了，所以第二个 promise 则成为了结果。在第一个 fulfilled 的 promise “赢得比赛”后，所有进一步的结果都将被忽略。

## 8 Promise.resolve

在现代的代码中，很少需要使用 Promise.resolve 和 Promise.reject 方法，因为 async/await 语法使它们变得有些过时了。

`Promise.resolve(value)` 用结果 `value` 创建一个 resolved 的 promise。

语法：`let promise = new Promise(resolve => resolve(value));`

大概的使用场景：当一个函数被期望返回一个 promise 时，这个方法用于兼容性。（译注：这里的兼容性是指，我们直接从缓存中获取了当前操作的结果 `value`，但是期望返回的是一个 promise，所以可以使用 `Promise.resolve(value)` 将 `value` “封装”进 promise，以满足期望返回一个 promise 的这个需求。）

## 9 Promise.reject

`Promise.reject(error)` 用 `error` 创建一个 rejected 的 promise。

语法：`let promise = new Promise((resolve, reject) => reject(error));`

实际上，这个方法几乎从未被使用过。



总结：

- `catch` 处理 promise 中的各种 error：在 `reject()` 调用中的，或者在处理程序中抛出的 error。
- 如果给定 `.then` 的第二个参数（即 error 处理程序），那么 `.then` 也会以相同的方式捕获 error。
- 在任何情况下我们都应该有 `unhandledrejection` 事件处理程序（用于浏览器，以及其他环境的模拟），以跟踪未处理的 error 并告知用户（可能还有我们的服务器）有关信息，以使我们的应用程序永远不会“死掉”。

​	

- `Promise` 类有 6 种静态方法：`Promise.all` 可能是在实战中使用最多的。











