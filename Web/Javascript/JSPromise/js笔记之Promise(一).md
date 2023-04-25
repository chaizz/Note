---
title: js笔记之Promise(一)
author: chaizz
date: 2023-3-22
tags: JavaScript
photo: ["https://origin.chaizz.com/ec55444c4a1211edac740242ac190002.png"]
---

​          

<!--more-->

# js笔记之Promise(一)



## 1 Promise 对象的构造器语法

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

![image-20230322204449440](https://origin.chaizz.com/tc/image-20230322204449440.png)

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

一个被 executor 完成的工作只能有一个结果或一个 error。并且，`resolve/reject` 只需要一个参数（或不包含任何参数），并且将忽略额外的参数。

reject接受的参数，可已接收任何类型， 但是建议使用Error对象或者继承Error的对象，因为这样做也是正常的做法。

实际上executor是异步执行的某些操作，并且在一段事件之后调用resolve/reject，但是这不是必须的，我们还可以立即调用他们，例如：

```js
let promise = new Promise(function (resolve, reject) {
    // 不花时间去做这项工作
    resolve(123); // 立即给出结果：123
});
```



## 2 then

Promise对象的state和result属性都是内部的，我们无法直接访问他们，但是我们可以对他们使用.then/ .catch/ .finally 

Promise的executor接受结果可以通过 .then .catch 方法注册消费函数。

then 

首先最重要最基础的就是.then，语法如下：

```js
promise.then(
    function (result) { /* handle a successful result */ },
    function (error) { /* handle an error */ }
);
```

`.then` 的第一个参数是一个函数，该函数将在 promise resolved 且接收到结果后执行。

`.then` 的第二个参数也是一个函数，该函数将在 promise rejected 且接收到 error 信息后执行。

例如在成功的情况下：

```js
let promise = new Promise(function (resolve, reject) {
    setTimeout(() => resolve("done!"), 1000);
});

// resolve 运行 .then 中的第一个函数
promise.then(
    result => alert(result), // 1 秒后显示 "done!"
    error => alert(error) // 不运行
);

```

或者在失败的情况下：

```js
let promise = new Promise(function (resolve, reject) {
    setTimeout(() => reject(new Error("Whoops!")), 1000);
});

// reject 运行 .then 中的第二个函数
promise.then(
    result => alert(result), // 不运行
    error => alert(error) // 1 秒后显示 "Error: Whoops!"
);
```



## 3 catch

如果我们只对error感兴趣，那么我们可以使用null作为第一个参数：.then(null, errorHandlingFunction)，或者我们也可以使用.catch(errorHandlingFunction)。

```js
let promise = new Promise((resolve, reject) => {
    setTimeout(() => reject(new Error("Whoops!")), 1000);
});

// .catch(f) 与 promise.then(null, f) 一样
promise.catch(alert); // 1 秒后显示 "Error: Whoops!"
```



## 4 finally 清理

想常规的try...catch...finally 一样， promise中也有finally，调用.finally类似于.then(f, f) 因为当 promise settled 时 f 就会执行：无论 promise 被 resolve 还是 reject。`finally` 的功能是设置一个处理程序在前面的操作完成后，执行清理/终结。例如:

```js
new Promise((resolve, reject) => {
    /* 做一些需要时间的事，之后调用可能会 resolve 也可能会 reject */
})
    // 在 promise 为 settled 时运行，无论成功与否
    .finally(() => stop loading indicator)
    // 所以，加载指示器（loading indicator）始终会在我们继续之前停止
    .then(result => show result, err => show error)
```



> `finally(f)` 并不完全是 `then(f,f)` 的别名。
>
> 1. finally处理程序没有参数，在finnally中我们不知道promise是否成功，没关系，因为我们的任务通常是执行常规的完成程序。就比如上面的例子，`finally` 处理程序没有参数，promise 的结果由下一个处理程序处理。
>
> 2. finally处理程序将结果或者是error传递给下一个合适的处理程序。
>
>    ```js
>    new Promise((resolve, reject) => {
>        setTimeout(() => resolve("value"), 2000)
>    })
>        .finally(() => alert("Promise ready")) // 先触发
>        .then(result => alert(result)); // <-- .then 显示 "value"
>    ```
>
>    在这个例子中，promise返回的value通过finally传递给了then。当然也有失败的例子：
>
>    ```js
>    new Promise((resolve, reject) => {
>        throw new Error("error");
>    })
>        .finally(() => alert("Promise ready")) // 先触发
>        .catch(err => alert(err));  // <-- .catch 显示这个 error
>    ```
>
> 3. finally 处理程序也不应该返回任何的内容，如果他返回了 返回的值也会被忽略。唯一例外的是当finally抛出error时， 这个error会被转到下一个处理程序。
>
> 总结：
>
> - `finally` 处理程序没有得到前一个处理程序的结果（它没有参数）。而这个结果被传递给了下一个合适的处理程序。
> - 如果 `finally` 处理程序返回了一些内容，那么这些内容会被忽略。
> - 当 `finally` 抛出 error 时，执行将转到最近的 error 的处理程序。



Promise 可以随时添加处理程序（handler）：如果结果已经在了，它们就会执行。

例如Promise编写异步代码的更多的示例：

```js
function loadScript(src) {
    return new Promise(function (resolve, reject) {
        let script = document.createElement('script');
        script.src = src;

        script.onload = () => resolve(script);
        script.onerror = () => reject(new Error(`Script load error for ${src}`));

        document.head.append(script);
    });
}
```

我们可以这样使用这个函数：

```js
promise.then(
    script => alert(`${script.src} is loaded!`),
    error => alert(`Error: ${error.message}`)
);

promise.then(script => alert('Another handler...'));
```
