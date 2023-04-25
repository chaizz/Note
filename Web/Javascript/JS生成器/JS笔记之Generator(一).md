---
title: JS笔记之Generator(一)
author: chaizz
date: 2023-3-27
tags: JavaScript
photo: ["https://origin.chaizz.com/ec55444c4a1211edac740242ac190002.png"]
---

​         

<!--more-->

# JS笔记之Generator(一)

在JS中常规函数只会返回一个单一值，或者不返回值。而generator可以按需一个接一个的返回（yield）多个值。



## 1 generator 函数

要创建一个generator，我们需要创建一个特殊的语法结构：function*，即所谓的“generator function”。它看起来像这样：

```js
function* generateSequence() {
    yield 1;
    yield 2;
    return 3;
}
```

generator 函数与常规函数的行为不同。在此类函数被调用时，它不会运行其代码。而是返回一个被称为 “generator object” 的特殊对象，来管理执行流程。

```js
function* generateSequence() {
    yield 1;
    yield 2;
    return 3;
}

// "generator function" 创建了一个 "generator object"
let generator = generateSequence();
console.log(generator); // [object Generator] {}
```

一个generator的主要的方法就是next()，当调用next()方法时，函数会执行到最近的一个yield，然后函数执行暂停，并将产出的（yielded）值返回到外部代码。

`next()` 的结果始终是一个具有两个属性的对象：

- `value`: 产出的（yielded）的值。
- `done`: 如果 generator 函数已执行完成则为 `true`，否则为 `false`。

例如我们获取第一个值：

```js
function* generateSequence() {
    yield 1;
    yield 2;
    return 3;
}
let generator = generateSequence();
let one = generator.next();
console.log(JSON.stringify(one)); // {value: 1, done: false}
```

所以到现在函数只执行到了 `yield 1;`  如果再次调用就会返回{value: 2, done: false}, 继续调用 就会返回{value: 3, done: true}，代表函数执行完毕，如果在次调用next()，没有任何意义。只会返回{done: true}

```js
function* generateSequence() {
    yield 1;
    yield 2;
    return 3;
}

let generator = generateSequence();

let one = generator.next();
console.log(JSON.stringify(one)); // {value: 1, done: false}

let two = generator.next();
console.log(JSON.stringify(two)); // {value: 1, done: false}

let t = generator.next();
console.log(JSON.stringify(t)); // {value: 1, done: false}

let f = generator.next();
console.log(JSON.stringify(f)); // {value: 1, done: false}
```

> **`function* f(…)` 或 `function *f(…)`？**
>
> 这两种语法都是对的。
>
> 但是通常更倾向于第一种语法，因为星号 `*` 表示它是一个 generator 函数，它描述的是函数种类而不是名称，因此 `*` 应该和 `function` 关键字紧贴一起。



## 2 generator 是可迭代的

我们可以用for...of 循环遍历生成器的值。

```js
function* generateSequence() {
    yield 1;
    yield 2;
    return 3;
}

let generator = generateSequence();

for (let value of generator) {
    console.log(value); // 1，然后是 2
}
```



**但是**上面的例子不会返回3，这是因为当 done: true 时，for..of 循环会忽略最后一个 value。因此，如果我们想要通过 for..of 循环显示所有的结果，我们必须使用 yield 返回它们：

```js
function* generateSequence() {
  yield 1;
  yield 2;
  yield 3;
}
```



因为 generator 是可迭代的，我们可以使用 iterator 的所有相关功能，例如：spread 语法 `...`：

```js
function* generateSequence() {
  yield 1;
  yield 2;
  yield 3;
}

let sequence = [0, ...generateSequence()];

alert(sequence); // 0, 1, 2, 3
```



## 3 使用 generator 进行迭代

在之前的文章中，我们手动创建了一个可迭代对象：

```js
let range = {
    from: 1,
    to: 5,

    // for..of range 在一开始就调用一次这个方法
    [Symbol.iterator]() {
        // ...它返回 iterator object：
        // 后续的操作中，for..of 将只针对这个对象，并使用 next() 向它请求下一个值
        return {
            current: this.from,
            last: this.to,

            // for..of 循环在每次迭代时都会调用 next()
            next() {
                // 它应该以对象 {done:.., value :...} 的形式返回值
                if (this.current <= this.last) {
                    return { done: false, value: this.current++ };
                } else {
                    return { done: true };
                }
            }
        };
    }
};

// 迭代整个 range 对象，返回从 `range.from` 到 `range.to` 范围的所有数字
alert([...range]); // 1,2,3,4,5
```

现在我们可以使用生成器来改造它：

```js
let range = {
    from: 1,
    to: 5,
    *[Symbol.iterator](){  // [Symbol.iterator]: function*() 的简写形式
        for(let value = this.from; value <= this.to; value++) {
            yield value
        }
    }
}

// 迭代整个 range 对象，返回从 `range.from` 到 `range.to` 范围的所有数字
console.log([...range]); // 1,2,3,4,5
```



## 4 generator 组合

generator 组合（composition）是 generator 的一个特殊功能，它允许透明地（transparently）将 generator 彼此“嵌入（embed）”到一起。

```js
function* generateSequence(start, end) {
    for (let i = start; i <= end; i++) yield i;
}

function* generatePasswordCodes() {

    // 0..9
    yield* generateSequence(48, 57);

    // A..Z
    yield* generateSequence(65, 90);

    // a..z
    yield* generateSequence(97, 122);

}

let str = '';

for (let code of generatePasswordCodes()) {
    str += String.fromCharCode(code);
}

console.log(str); // 0..9A..Za..z
```

在上述的代码中使用了yield*指令，`yield*` 指令将执行 **委托** 给另一个 generator。这个术语意味着 `yield* gen` 在 generator `gen` 上进行迭代，并将其产出（yield）的值透明地（transparently）转发到外部。就好像这些值就是由外部的 generator yield 的一样。

> generator 组合（composition）是将一个 generator 流插入到另一个 generator 流的自然的方式。它不需要使用额外的内存来存储中间结果。



## 5 yield 是一条双向路



generator和可迭代对象很相似，但是generator更加的强大和灵活。其中generator不仅可以向外部返回结果，也可以将外部的值传递给generator内。通过调用generator.next(arg)，就能给generator添加值。这个arg就会变成yield的结果，例如：

```js
function* gen() {
    // 向外部代码传递一个问题并等待答案
    let result = yield "2 + 2 = ?"; // (*)

    console.log(result);
}
let generator = gen();
let question = generator.next().value; // <-- yield 返回的 value, question= 2 + 2 = ?

generator.next(4); // --> 将结果传递到 generator 中
```



## 6 generator.throw

在generator中也有可能抛出一个异常，error本身也是一个结果。如果要向 `yield` 传递一个 error，我们应该调用 `generator.throw(err)`。

正常的错误像之前的错误一样，会调出generator, 使脚本停止。



## 7 generator.return

`generator.return(value)` 完成 generator 的执行并返回给定的 `value`。例如：

```js
function * gen() {
    yield 1;
    yield 2;
    yield 3;
}

const g = gen();

console.log(g.next());        // { value: 1, done: false }
console.log(g.return('foo'));; // { value: "foo", done: true }
console.log(g.next());;        // { value: undefined, done: true }
```



如果我们在已完成的 generator 上再次使用 `generator.return()`，它将再次返回该值（[MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Generator/return)）。

通常我们不使用它，因为大多数时候我们想要获取所有的返回值，但是当我们想要在特定条件下停止 generator 时它会很有用。



## 8 总结

- generator 是通过 generator 函数 `function* f(…) {…}` 创建的。
- 在 generator（仅在）内部，存在 `yield` 操作。
- 外部代码和 generator 可能会通过 `next/yield` 调用交换结果。

在现代 JavaScript 中，generator 很少被使用。但有时它们会派上用场，因为函数在执行过程中与调用代码交换数据的能力是非常独特的。而且，当然，它们非常适合创建可迭代对象。











