---
title: JS笔记之Generator(二)
author: chaizz
date: 2023-3-30
tags: JavaScript
photo: ["https://origin.chaizz.com/ec55444c4a1211edac740242ac190002.png"]
---

​         

<!--more-->

# JS笔记之Generator(二)



异步迭代允许我们按照异步的方式来取生成器的中的数据。我们通过网络分段下载数据时，异步生成器更加方便。

## 1 可迭代对象

如果我们想让一个对象可迭代我们需要在对象中设置Symbol.iterator的特殊方法：

- 该方法被 `for..of` 结构调用，并且它应该返回一个带有 `next` 方法的对象。
- 对于每次迭代，都会为下一个值调用 `next()` 方法。
- `ext()` 方法应该以 `{done: true/false, value:<loop value>}` 的格式返回一个值，其中 `done:true` 表示循环结束。

```js
let range = {
    from: 1,
    to: 5,

    [Symbol.iterator]() { // 在 for..of 循环开始时被调用一次
        return {
            current: this.from,
            last: this.to,

            next() { // 每次迭代时都会被调用，来获取下一个值
                if (this.current <= this.last) {
                    return { done: false, value: this.current++ };
                } else {
                    return { done: true };
                }
            }
        };
    }
};

for (let value of range) {
    console.log(value); // 1，然后 2，然后 3，然后 4，然后 5
}
```



## 2 异步可迭代对象

当值是以异步的方式出现时，例如在 `setTimeout` 或者另一种延迟之后，就需要异步迭代。具体的实现方法：

- 使用 `Symbol.asyncIterator` 取代 `Symbol.iterator`。
- `next()` 方法应该返回一个 `promise`（带有下一个值，并且状态为 `fulfilled`）
  - 关键字 `async` 可以实现这一点，我们可以简单地使用 `async next()`。
- 迭代异步可迭代对象使用for await (let item of iterable)` 循环来迭代这样的对象。

```js
let range = {
    from: 1,
    to: 5,

    [Symbol.asyncIterator]() { // (1)
        return {
            current: this.from,
            last: this.to,

            async next() { // (2)

                // 注意：我们可以在 async next 内部使用 "await"
                await new Promise(resolve => setTimeout(resolve, 1000)); // (3)

                if (this.current <= this.last) {
                    return { done: false, value: this.current++ };
                } else {
                    return { done: true };
                }
            }
        };
    }
};

(async () => {

    for await (let value of range) { // (4)
        console.log(value); // 1,2,3,4,5
    }

})()
```



>**Spread 语法** `...` **无法异步工作**
>
>```javascript
>alert( [...range] ); // Error, no Symbol.iterator
>```
>
>这很正常，因为它期望找到 `Symbol.iterator`，而不是 `Symbol.asyncIterator`。
>
>`for..of` 的情况和这个一样：没有 `await` 关键字时，则期望找到的是 `Symbol.iterator`。



## 3 generator

Generator 是标有 `function*`（注意星号）的函数，它使用 `yield` 来生成值，并且我们可以使用 `for..of` 循环来遍历它们。

```js
function* generateSequence(start, end) {
    for (let i = start; i <= end; i++) {
        yield i;
    }
}

for (let value of generateSequence(1, 5)) {
    console.log(value); // 1，然后 2，然后 3，然后 4，然后 5
}
```

一般的可迭代对象都是返回一个生成器，可以使代码更短：

```js
let range = {
    from: 1,
    to: 5,

    *[Symbol.iterator]() { // [Symbol.iterator]: function*() 的一种简写
        for (let value = this.from; value <= this.to; value++) {
            yield value;
        }
    }
};

for (let value of range) {
    console.log(value); // 1，然后 2，然后 3，然后 4，然后 5
}
```



## 4 异步generator

对于大多数的实际应用程序，当我们想创建一个异步生成一系列值的对象时，我们都可以使用异步 generator。

语法也比较简单就是在 `function*` 前面加上 `async`。这即可使 generator 变为异步的。然后使用for...await 调用。

```js
async function* generateSequence(start, end) {

    for (let i = start; i <= end; i++) {

        // 哇，可以使用 await 了！
        await new Promise(resolve => setTimeout(resolve, 1000));

        yield i;
    }

}

(async () => {

    let generator = generateSequence(1, 5);
    for await (let value of generator) {
        console.log(value); // 1，然后 2，然后 3，然后 4，然后 5（在每个 console.log 之间有延迟）
    }

})();
```

因为此 generator 是异步的，所以我们可以在其内部使用 `await`，依赖于 `promise`，执行网络请求等任务。

如果想要获取异步的生成器的下一个值，要是用await generator.next()



## 5 实际的案例:分页数据

目前，有很多在线服务都是发送的分页的数据（paginated data）。例如，当我们需要一个用户列表时，一个请求只返回一个预设数量的用户（例如 100 个用户）—— “一页”，并提供了指向下一页的 URL。

例如，GitHub 允许使用相同的分页提交（paginated fashion）的方式找回 commit：

- 我们应该以 `https://api.github.com/repos/<repo>/commits` 格式创建进行 `fetch` 的网络请求。
- 它返回一个包含 30 条 commit 的 JSON，并在返回的 `Link` header 中提供了指向下一页的链接。
- 然后我们可以将该链接用于下一个请求，以获取更多 commit，以此类推。

我们创建一个函数 `fetchCommits(repo)`，用来在任何我们有需要的时候发出请求，来为我们获取 commit。并且，该函数能够关注到所有分页内容。对于我们来说，它将是一个简单的 `for await..of` 异步迭代。

用法如下：

```js
for await (let commit of fetchCommits("username/repository")) {
  // 处理 commit
}
```

```js
async function* fetchCommits(repo) {
    let url = `https://api.github.com/repos/${repo}/commits`;

    while (url) {
        const response = await fetch(url, { // (1)
            headers: { 'User-Agent': 'Our script' }, // github 需要任意的 user-agent header
        });

        const body = await response.json(); // (2) 响应的是 JSON（array of commits）

        // (3) 前往下一页的 URL 在 header 中，提取它
        let nextPage = response.headers.get('Link').match(/<(.*?)>; rel="next"/);
        nextPage = nextPage?.[1];

        url = nextPage;

        for (let commit of body) { // (4) 一个接一个地 yield commit，直到最后一页
            yield commit;
        }
    }
}

```



## 6 总结

常规的 iterator 和 generator 可以很好地处理那些不需要花费时间来生成的的数据。

当我们期望异步地，有延迟地获取数据时，可以使用它们的异步版本，并且使用 `for await..of` 替代 `for..of`。

异步 iterator 与常规 iterator 在语法上的区别：

|                          | Iterable                      | 异步 Iterable                                         |
| :----------------------- | :---------------------------- | :---------------------------------------------------- |
| 提供 iterator 的对象方法 | `Symbol.iterator`             | `Symbol.asyncIterator`                                |
| `next()` 返回的值是      | `{value:…, done: true/false}` | resolve 成 `{value:…, done: true/false}` 的 `Promise` |

异步 generator 与常规 generator 在语法上的区别：

|                     | Generator                     | 异步 generator                                        |
| :------------------ | :---------------------------- | :---------------------------------------------------- |
| 声明方式            | `function*`                   | `async function*`                                     |
| `next()` 返回的值是 | `{value:…, done: true/false}` | resolve 成 `{value:…, done: true/false}` 的 `Promise` |

在 Web 开发中，我们经常会遇到数据流，它们分段流动（flows chunk-by-chunk）。例如，下载或上传大文件。

我们可以使用异步 generator 来处理此类数据。值得注意的是，在一些环境，例如浏览器环境下，还有另一个被称为 Streams 的 API，它提供了特殊的接口来处理此类数据流，转换数据并将数据从一个数据流传递到另一个数据流（例如，从一个地方下载并立即发送到其他地方）。





