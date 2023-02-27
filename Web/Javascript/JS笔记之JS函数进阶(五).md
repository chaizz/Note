---
title: JS笔记之JS函数进阶(五)
author: chaizz
date: 2023-2-20
tags: JavaScript
photo: ["https://tc.chaizz.com/ec55444c4a1211edac740242ac190002.png"]

---

​    

<!--more-->

JS笔记之JS函数进阶(五)





JS函数应用缓存之透明缓存

当我们有一个函数运行非常慢且每次结果都一致的时候，可以用到包装器函数，用来保存他的运行结果。我们可以使用一个Map 对象，来保存函数第一次运行的结果。

```js
function slow(x) {
    // 这里可能会有重负载的 CPU 密集型工作
    console.log(`Called with ${x}`);
    return x;
}

function cachingDecorator(func) {
    let cache = new Map();

    return function (x) {
        if (cache.has(x)) {    // 如果缓存中有对应的结果
            return cache.get(x); // 从缓存中读取结果
        }

        let result = func(x);  // 否则就调用 func

        cache.set(x, result);  // 然后将结果缓存（记住）下来
        return result;
    };
}

slow = cachingDecorator(slow);

console.log(slow(1)); // slow(1) 被缓存下来了，并返回结果
console.log("Again: " + slow(1)); // 返回缓存中的 slow(1) 的结果

console.log(slow(2)); // slow(2) 被缓存下来了，并返回结果
console.log("Again: " + slow(2)); // 返回缓存中的 slow(2) 的结果
```

上述代码段中：`cachingDecorator`方法中定义了一个map对象，然后返回了一个方法。Map对象的作用就是判断耗时的函数是否已经被调用过。返回的函数接受一个参数值，该值就是耗时函数的值，在函数中首先判断Map中是否有当前参数的一个对象，如果有则直接返回Map对象的值，如果没有则继续执行耗时函数，并把结果添加到Map对象中。

我们可以将`cachingDecorator`称为装饰器，装饰器是一个特殊的函数，他接收一个函数，并返回一个函数。

装饰器有几个好处：

- 装饰器`cachingDecorator`是可重用的，我们可以将它应用于另外一个函数。
- 缓存的逻辑是独立的，我们并没有修改耗时函数的复杂性。
- 装饰器可以进行多个组合。



在使用上面的装饰器时，有时我们可能会遇到一个问题，当这个耗时方法在对象中是，且在函数中使用`this`调用了对像中的其他的属性，例如一个方法 `someMethod`，我们要对这个对象的方法属性应用装饰器，那么就会出现如下的问题：

`TypeError: this.someMethod is not a function`



```js
let worker = {
    someMethod() {
        return 1;
    },

    slow(x) {
        // 可怕的 CPU 过载任务
        console.log("Called with " + x);
        return x * this.someMethod(); // (*)
    }
};

// 和之前例子中的代码相同
function cachingDecorator(func) {
    let cache = new Map();
    return function (x) {
        if (cache.has(x)) {
            return cache.get(x);
        }
        let result = func(x); // (**)
        cache.set(x, result);
        return result;
    };
}

console.log(worker.slow(1)); // 原始方法有效

worker.slow = cachingDecorator(worker.slow); // 现在对其进行缓存

console.log(worker.slow(2)); // TypeError: this.someMethod is not a function
```

出现上面的问题，是因为此时好使函数中的`this`已经不再是原来的`worker`对象了，他指向了`global`，在全局对象中，自然没有`someMethod `这个方法。

当我们将`worker.slow`赋值给一个变量时，也会出现上面的错误。

```js
let func = worker.slow;
func(2); // TypeError: this.someMethod is not a function
```



使用 “func.call” 设定上下文

他允许调用一个显示设置this的函数，运行func，提供第一参数作为该函数的this对象，后端面的作为参数。

```js
function sayHi() {
    console.log(this.name);
  }
  
  let user = { name: "John" };
  let admin = { name: "Admin" };
  
  // 使用 call 将不同的对象传递为 "this"
  sayHi.call( user ); // John
  sayHi.call( admin ); // Admin
```

在上述的包装器中使用`func.call()` 来解决`TypeError: this.someMethod is not a function` 的错误。

```js
let worker = {
    someMethod() {
        return 1;
    },

    slow(x) {
        console.log("Called with " + x);
        return x * this.someMethod(); // (*)
    }
};

function cachingDecorator(func) {
    let cache = new Map();
    return function (x) {
        if (cache.has(x)) {
            return cache.get(x);
        }
        let result = func.call(this, x); // 现在 "this" 被正确地传递了
        cache.set(x, result);
        return result;
    };
}

worker.slow = cachingDecorator(worker.slow); // 现在对其进行缓存

console.log(worker.slow(2)); // 工作正常
console.log(worker.slow(2)); // 工作正常，没有调用原始函数（使用的缓存）
```

上述`func.call()`的调佣详细过程：

1. `cachingDecorator` 装饰器函数接受的方法是 `worker.slow`。
2. 此时， 装饰器中的 `function(x)` 就是`worker.slow`。
3. 当执行`worker.slow(2)`，`this` 就是的方法前的对象，也就是`.`之前的对象`worker`。



传递多个参数

上述中的装饰器只接受一个参数，我们使用一个参数作为Map对象的key，但是当有多个参数时如何存储缓存呢？

1. 自定义一个可以使用多个参数作为key的Map对象。
2. 使用嵌套Map，即一个 cache.get(x) 是另一个Map的key cache.get(x).get(b)。
3. 将两个值合并为一个，并将其hash。

第三种实现方式：

```js
let worker = {
    slow(min, max) {
        console.log(`Called with ${min},${max}`);
        return min + max;
    }
};

function cachingDecorator(func, hash) {
    let cache = new Map();
    return function () {
        let key = hash(arguments); // (*)
        if (cache.has(key)) {
            return cache.get(key);
        }

        let result = func.call(this, ...arguments); // (**)

        cache.set(key, result);
        return result;
    };
}

function hash(args) {
    return args[0] + ',' + args[1];
}

worker.slow = cachingDecorator(worker.slow, hash);

console.log(worker.slow(3, 5)); // works
console.log("Again " + worker.slow(3, 5)); // same (cached)
```



















