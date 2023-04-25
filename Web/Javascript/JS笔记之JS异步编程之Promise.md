---
title: JS笔记之JS异步编程之Promise
author: chaizz
date: 2022-10-13 17:40:18
tags: JavaScript
photo: ["https://origin.chaizz.com/ec55444c4a1211edac740242ac190002.png"]
---

​          

<!--more-->

# 异步编程

> 异步编程技术使你的程序可以在执行一个可能长期运行的任务的同时继续对其他事件做出反应而不必等待任务完成。与此同时，你的程序也将在任务完成后显示结果。



浏览器在执行js代码时，是按照书写的顺序一行一行的执行，会等待代码的解析和工作，在上一行执行完毕后才会执行下一行。执行同步函数时也是如此。

单当遇到一个耗时的同步函数时，操作会非常消耗时间且我们无法做其他事情。

我们希望我们的程序可以：

- 通过调用一个函数来启动一个长期运行的操作。
- 然函数开始 操作时立即返回，这样我们的程序就可以保持对其他事件做出反应能力。
- 单操作完成时通知我们操作的结果。

以上的功能是异步寒函数为我们提供的能力。



我们常见的事件处理程序就是异步编程的一种形式。即提供的函数，将在事件发生时被调用（而不是立即被用用），如果事件操作已经完成，那么就可以看到事件是如何被用来通知调用者异步函数调用的结果的。

例如一些早期的API：[XMLHttpRequest](https://developer.mozilla.org/zh-CN/docs/Web/API/XMLHttpRequest),可以通过给 `XMLHttpRequest` 对象附加事件监听器来让程序在请求进展和最终完成时获得通知。



事件处理程序是一种特殊的回调函数，而回调函数则是一个被传递到另一个函数中的会在合适的时候被调用的函数。回调函数曾经是 JavaScript 中实现异步函数的主要方式。

然而回调函数本身也接受被回调，基于回调的代码则会非常难理解。例如：

```js
function doStep1(init, callback) {
    const result = init + 1;
    callback(result);
}

function doStep2(init, callback) {
    const result = init + 2;
    callback(result);
}

function doStep3(init, callback) {
    const result = init + 3;
    callback(result);
}

function doOperation() {
    doStep1(0, result1 => {
        doStep2(result1, result2 => {
            doStep3(result2, result3 => {
                console.log(`结果：${result3}`);
            });
        });
    });
}

doOperation();
```

由于以上原因大多数现代的异步API都不使用回调，事实上JS中的异步编程的基础是 Promise 。

# Promise

Promise 是现代js中异步编程的基础，是一个由异步函数返回的 可以向我们指示当前操作所处的 对象的状态。在 Promise 返回给调用者的时候，操作往往还没有完成，但是 Promise 对象可以让我们操作最终完成时对其处理（无论成功还是失败）。

在基于 Promise 的API中，异步函数会启动操作并返回 Promise 对象，然后，你可以将处理函数附加到 Promise 对象上，当操作完成时（成功或者失败），这些处理函数将会执行。



示例：使用fetch()API 来解释promise。

打开网站：[https://example.org](https://example.org/) 在控制台中输入以下的代码：

```json
// 调用 fetch() API，并将返回值赋给 fetchPromise 变量。
const fetchPromise = fetch('https://mdn.github.io/learning-area/javascript/apis/fetching-data/can-store/products.json');

// 紧接着，输出 fetchPromise 变量，输出结果应该像这样：Promise { <state>: "pending" }。这告诉我们有一个 Promise 对象，它有一个 state属性，值是 "pending"。"pending" 状态意味着操作仍在进行中。
console.log(fetchPromise);

// 将一个处理函数传递给 Promise 的 then() 方法。当（如果）获取操作成功时，Promise 将调用我们的处理函数，传入一个包含服务器的响应的 Response 对象。
fetchPromise.then( response => {
  console.log(`已收到响应：${response.status}`);
});

// 输出一条信息，说明我们已经发送了这个请求。
console.log("已发送请求……");
```

控制台的响应结果应该是这样的：

```js
Promise { <state>: "pending" }
已发送请求……
已收到响应：200
```

我们可以看到已发送请求在响应之前输出了，如果是同步函数，则会在最后返回【已发送请求……】。



当通过 fetch()API得到一个Response对象时，需要调用另一个函数来接受这个响应，这次我们想要得到json格式的数据，可以调用Response.json()方法。 json()也是也是一个异步函数，因此我们连续调用两个异步函数。

```json
const fetchPromise = fetch('https://mdn.github.io/learning-area/javascript/apis/fetching-data/can-store/products.json');

fetchPromise.then( response => {
  const jsonPromise = response.json();
  jsonPromise.then( json => {
    console.log(json);
  });
});
```



在这个示例中fetch()返回的 Promise 对象的then()方法，调用了response.json()方法，json()同样返回了一个 Promise 对象。 jsonPromise的then()方法输出了json的内容。

以上的代码看起来和之前的多层级回调差不多，确实是这样。但是 Promise 的优雅之处在于then()也是返回一个 Promise 对象，这个 Promise 将指示 `then()` 中调用的异步函数的完成状态。这意味着我们可以（当然也应该）把上面的代码改写成这样：

```json
const fetchPromise = fetch('https://mdn.github.io/learning-area/javascript/apis/fetching-data/can-store/products.json');

fetchPromise
    .then(response => {
        return response.json();
    })
    .then(json => {
        console.log(json[0].name);
    });
```



我们不必在第一个then()方法里面写另外一个then()方法，我们可以直接返回json()返回的 Promise ,并且在改返回值上调用第二个then()，这种调用方式叫做**Promise链**。意味着当我们连续调用异步函数的时候就可以避免嵌套带来的代码不美观易懂。



## 错误捕获

上述的一个response请求示例是一个理想的代码，因为没有添加异常的处理。Promise 对象中提供了一个catch()方法来处理错误，她很像then()，可以调用它并传入一个处理函数。然后，当异步操作*成功*时，传递给then()的处理函数被调用，而当异步操作*失败*时，传递给catch()的处理函数被调用。

如果将catch()添加到 Promise 链的末尾，它就可以在任何异步函数失败时被调用。于是，我们就可以将一个操作实现为几个连续的异步函数调用，并在一个地方处理所有错误。

例如：

```json
const fetchPromise = fetch('bad-scheme://mdn.github.io/learning-area/javascript/apis/fetching-data/can-store/products.json');

fetchPromise
    .then(response => {
        if (!response.ok) {
            throw new Error(`HTTP 请求错误：${response.status}`);
        }
        return response.json();
    })
    .then(json => {
        console.log(json[0].name);
    })
    .catch(error => {
        console.error(`无法获取产品列表：${error}`);
    });
```



## Promise 术语

首先 Promise 有三种状态：

- **待定（pending）**：初始状态，既没有被兑现，也没有被拒绝。这是调用fetch()返回 Promise 时的状态，此时请求还在进行中。
- **已兑现（fulfilled）**：意味着操作成功完成。当 Promise 完成时，它的then()处理函数被调用。
- **已拒绝（rejected）**：意味着操作失败。当一个 Promise 失败时，它的catch()处理函数被调用。

有时我们用 **已敲定（settled）** 这个词来同时表示 **已兑现（fulfilled）** 和 **已拒绝（rejected）** 两种情况。

如果一个 Promise 处于已决议（resolved）状态，或者它被“锁定”以跟随另一个 Promise 的状态，那么它就是 **已兑现（fulfilled）**。

文章 [Let's talk about how to talk about promises](https://thenewtoys.dev/blog/2021/02/08/lets-talk-about-how-to-talk-about-promises/) 对这些术语的细节做了很好的解释。

> 注：这里的成功或者失败取决于使用的API。例如：fetch()认为服务器返回一个错误（如[404 Not Found](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Status/404)）时请求成功，但如果网络错误阻止请求被发送，则认为请求失败。

文章 [Let's talk about how to talk about promises](https://thenewtoys.dev/blog/2021/02/08/lets-talk-about-how-to-talk-about-promises/) 对这些术语的细节做了很好的解释。



## 合并使用多个 Promise

当操作有几个异步函数组成时，而且需要在下一个函数开始之前完成每一个函数，需要的就是 Promise 链。但是在其他的一些情况下，可能需要合并多个异步函数的调用。

有时你需要所有的 Promise 都得到实现，但它们并不相互依赖。在这种情况下，==将它们一起启动然后在它们全部被兑现后得到通知会更有效率==。这里需要 [`Promise.all()`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Promise/all) 方法。它接收一个 Promise 数组，并返回一个单一的 Promise。

由 Promise.all() 返回的 Promise 有以下的特性：

1、当且仅当数组中所有的 Promise都被兑现时，才会通知then()函数，并提供一个包含所有响应的数组，数组中响应的顺序与被传入all()的 Promise 相等。

2、会被拒绝--如果数组中任何一个 Promise 被拒绝，此时 catch()处理函数被调用，并提供被拒绝的 Promise 所抛出的错误。

示例：

```js
const fetchPromise1 = fetch('https://mdn.github.io/learning-area/javascript/apis/fetching-data/can-store/products.json');
const fetchPromise2 = fetch('https://mdn.github.io/learning-area/javascript/apis/fetching-data/can-store/not-found');
const fetchPromise3 = fetch('bad-scheme://mdn.github.io/learning-area/javascript/oojs/json/superheroes.json');

Promise.all([fetchPromise1, fetchPromise2, fetchPromise3])
    .then(responses => {
        for (const response of responses) {
            console.log(`${response.url}：${response.status}`);
        }
    })
    .catch(error => {
        console.error(`获取失败：${error}`)
    });

```



有时，可能需要等待一组 Promise 中的某一个 Promise 的执行，而不关心是哪一个。在这种情况下，你需要 Promise.any()这就像Promise.all()，不过在 Promise 数组中的任何一个被兑现时它就会被兑现，如果所有的 Promise 都被拒绝，它也会被拒绝。

> 注：在这种情况下，我们无法预测哪个获取请求会先被兑现。



## async 和 await 

async 关键字提供了一种更简单的方法来处理基于异步 Promise 的代码，在一个函数的开头添加 async 可以使其成为一个异步函数。

```js
async function myFunction () {
    // 异步函数
}
```

在异步函数中，可以在调用一个返回 Promise 的函数之前使用 `await` 关键字。这使得代码在该点上等待，直到 Promise 被完成，这时 Promise 的响应被当作返回值，或者被拒绝的响应被作为错误抛出。

```js
async function fetchProducts() {
    try {
        // 在这一行之后，我们的函数将等待 `fetch()` 调用完成
        // 调用 `fetch()` 将返回一个“响应”或抛出一个错误
        const response = await fetch('https://mdn.github.io/learning-area/javascript/apis/fetching-data/can-store/products.json');
        if (!response.ok) {
            throw new Error(`HTTP 请求错误：${response.status}`);
        }
        // 在这一行之后，我们的函数将等待 `response.json()` 的调用完成
        // `response.json()` 调用将返回 JSON 对象或抛出一个错误
        const json = await response.json();
        console.log(json[0].name);
    } catch (error) {
        console.error(`无法获取产品列表：${error}`);
    }
}

fetchProducts();
```

上面的代码使用await关键字 让异步函数想同步函数一样去执行，但是，即使像同步函数那样也无法使用下面那样的代码进行操作：(因为异步函数总是返回一个 Promise)

```json
async function fetchProducts() {
    try {
        const response = await fetch('https://mdn.github.io/learning-area/javascript/apis/fetching-data/can-store/products.json');
        if (!response.ok) {
            throw new Error(`HTTP 请求错误：${response.status}`);
        }
        const json = await response.json();
        return json;
    } catch (error) {
        console.error(`无法获取产品列表：${error}`);
    }
}

const json = fetchProducts();
console.log(json[0].name);   // json 是一个 Promise 对象，因此这句代码无法正常工作
```



但是我们可以这样做：(因为异步函数返回一个 Promise 可以调用.then()方法)

```json
async function fetchProducts() {
    try {
        const response = await fetch('https://mdn.github.io/learning-area/javascript/apis/fetching-data/can-store/products.json');
        if (!response.ok) {
            throw new Error(`HTTP 请求错误：${response.status}`);
        }
        const json = await response.json();
        return json;
    } catch (error) {
        console.error(`无法获取产品列表：${error}`);
    }
}

const jsonPromise = fetchProducts();
jsonPromise.then((json) => console.log(json[0].name));

```



在实际开发过程中可能会在 Promise 链的地方使用async函数， await将会强制异步操作以串联的形式工作。如果下一个操作的结果取决于上一个操作的结果，这是非常必要的，反之 Promise.all() 这样的操作会有更好的性能。

## 总结

Promise 是现代JavaScript异步编程的基础，他避免的深度嵌套，是表达和理解异步操作变的更加容易，并且还支持一种类似于同步编程中 `try...catch` 语句的错误处理方式。

`async` 和 `await` 关键字使得从一系列连续的异步函数调用中建立一个操作变得更加容易，避免了创建显式 Promise 链，并允许你像编写异步代码那样编写同步代码。

Promise 在所有现代浏览器的最新版本中都可以使用；唯一会出现支持问题的地方是 Opera Mini 和 IE11 及更早的版本。





