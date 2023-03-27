---
title: js笔记之Promise(六)
author: chaizz
date: 2023-3-27
tags: JavaScript
photo: ["https://tc.chaizz.com/ec55444c4a1211edac740242ac190002.png"]
---

​          

<!--more-->

# js笔记之Promise(六)



async/await 是以更舒适的方式使用 promise 的一种特殊语法，同时它也非常易于理解和使用。



## 1 async function

语法：他可以被放置当在一个函数前面，他表达了一个简单的事情：即这个函数总是返回一个 promise。其他值将自动被包装在一个 resolved 的 promise 中。

```js
async function f() {
    return 1;
}
```

我们显式的返回一个promise结果也是一样的。

```js
async function f() {
    return Promise.resolve(1);
}

f().then(console.log); // 1
```

所以说async确保了函数返回一个promise，也会将非 promise 的值包装进去。



## 2 await

语法：只在 async 函数内工作, 让 JavaScript 引擎等待直到 promise 完成（settle）并返回结果。

```js
let value = await promise;
```

例如一个一秒后的resolve的promise:

```js
async function f() {

    let promise = new Promise((resolve, reject) => {
        setTimeout(() => resolve("done!"), 1000)
    });

    let result = await promise; // 等待，直到 promise resolve (*)

    console.log(result); // "done!"
}

f();
```



上面的这个函数在执行的时候，“暂停”在了 `(*)` 那一行，并在 promise settle 时，拿到 `result` 作为结果继续往下执行。所以上面这段代码在一秒后显示 “done!”。

> Tips：`await` 实际上会暂停函数的执行，直到 promise 状态变为 settled，然后以 promise 的结果继续执行。这个行为不会耗费任何 CPU 资源，因为 JavaScript 引擎可以同时处理其他任务：执行其他脚本，处理事件等。

相比于 `promise.then`，它只是获取 promise 的结果的一个更优雅的语法。并且也更易于读写。

> 如果我们尝试在非 async 函数中使用 `await`，则会报语法错误。



在现代浏览器中可以在一个modules，那么在顶层使用await也是可以的。

```js
*// 我们假设此代码在 module 中的顶层运行*
let response = await fetch('/article/promise-chaining/user.json');
let user = await response.json();
console.log(user);
```



如果我们没有使用 modules，或者必须兼容 旧版本浏览器 ，那么这儿还有一个通用的方法：包装到匿名的异步函数中。

```js
(async () => {
    let response = await fetch('/article/promise-chaining/user.json');
    let user = await response.json();
})();
```



> 像 `promise.then` 那样，`await` 允许我们使用 thenable 对象（那些具有可调用的 `then` 方法的对象）。这里的想法是，第三方对象可能不是一个 promise，但却是 promise 兼容的：如果这些对象支持 `.then`，那么就可以对它们使用 `await`。



## 3 在类class中使用async方法

在类Class中使用async方法，只需在对应方法前面加上 `async` 即可。

```js
class Waiter {
    async wait() {
        return await Promise.resolve(1);
    }
}

new Waiter()
    .wait()
    .then(alert); // 1（alert 等同于 result => alert(result)）
```



## 4 Error 处理

如果一个 promise 正常 resolve，`await promise` 返回的就是其结果。但是如果 promise 被 reject，它将 throw 这个 error，就像在这一行有一个 `throw` 语句那样。例如：

```js
async function f() {
  await Promise.reject(new Error("Whoops!"));
}

// 和上面的是一样的
async function f() {
    throw new Error("Whoops!");
}
```

在真实开发中，promise 可能需要一点时间后才 reject。在这种情况下，在 `await` 抛出（throw）一个 error 之前会有一个延时。

我们可以用 `try..catch` 来捕获上面提到的那个 error，与常规的 `throw` 使用的是一样的方式：

```js
async function f() {

    try {
        let response = await fetch('http://no-such-url');
    } catch (err) {
        console.log(err); // TypeError: failed to fetch
    }
}

f();
```

如果有 error 发生，执行控制权马上就会被移交至 `catch` 块。我们也可以用 `try` 包装多行 `await` 代码：

```js
async function f() {

    try {
        let response = await fetch('/no-user-here');
        let user = await response.json();
    } catch (err) {
        // 捕获到 fetch 和 response.json 中的错误
        alert(err);
    }
}

f();
```

如果我们没有 `try..catch`，那么由异步函数 `f()` 的调用生成的 promise 将变为 rejected。我们可以在函数调用后面添加 `.catch` 来处理这个 error：

```js
async function f() {
    let response = await fetch('http://no-such-url');
}

// f() 变成了一个 rejected 的 promise
f().catch(alert); // TypeError: failed to fetch // (*)
```



> **`async/await` 和 `promise.then/catch`**
>
> 当我们使用 `async/await` 时，几乎就不会用到 `.then` 了，因为 `await` 为我们处理了等待。并且我们使用常规的 `try..catch` 而不是 `.catch`。这通常（但不总是）更加方便。
>
> 但是当我们在代码的顶层时，也就是在所有 `async` 函数之外，我们在语法上就不能使用 `await` 了，所以这时候通常的做法是添加 `.then/catch` 来处理最终的结果（result）或掉出来的（falling-through）error，例如像上面那个例子中的 `(*)` 行那样。



> **`async/await` 可以和 `Promise.all` 一起使用**
>
> 当我们需要同时等待多个 promise 时，我们可以用 `Promise.all` 把它们包装起来，然后使用 `await`：
>
> ```javascript
> // 等待结果数组
> let results = await Promise.all([
>   fetch(url1),
>   fetch(url2),
>   ...
> ]);
> ```
>
> 如果出现 error，也会正常传递，从失败了的 promise 传到 `Promise.all`，然后变成我们能通过使用 `try..catch` 在调用周围捕获到的异常（exception）。



## 5 总结

函数前面的关键字 `async` 有两个作用：

1. 让这个函数总是返回一个 promise。
2. 允许在该函数内使用 `await`。

Promise 前的关键字 `await` 使 JavaScript 引擎等待该 promise settle，然后：

1. 如果有 error，就会抛出异常 —— 就像那里调用了 `throw error` 一样。
2. 否则，就返回结果。

这两个关键字一起提供了一个很好的用来编写异步代码的框架，这种代码易于阅读也易于编写。

有了 `async/await` 之后，我们就几乎不需要使用 `promise.then/catch`，但是不要忘了它们是基于 promise 的，因为有些时候（例如在最外层作用域）我们不得不使用这些方法。并且，当我们需要同时等待需要任务时，`Promise.all` 是很好用的。

