---
title: JS笔记之JS函数进阶(六)
author: chaizz
date: 2023-3-02
tags: JavaScript
photo: ["https://tc.chaizz.com/ec55444c4a1211edac740242ac190002.png"]
---

​        

<!--more-->

# JS笔记之JS函数进阶(六)





## 1 函数绑定

将对象方法作为回调进行绑定，例如传递给`setTimeout`会存在丢失this的问题。比如：

```js
let user = {
    firstName: "John",
    sayHi() {
        console.log(`Hello, ${this.firstName}!`);
    }
};

setTimeout(user.sayHi, 1000); // Hello, undefined!
```

这是因为`setTimeout` 获取到的函数 `user.sayHi` 和对象 `user` 分离了，无法在获取`user` 对象的上下文。

这是一种比较典型的问题：我们想将一个对象的方法，传递到别的地方调用，如何确保对象中的方法的上下文呢？



## 2 解决方案一：包装器

通过包装器从外部的此法环境，能够获取user，

```js
let user = {
    firstName: "John",
    sayHi() {
        console.log(`Hello, ${this.firstName}!`);
    }
};

setTimeout(function () {
    user.sayHi(); // Hello, John!
}, 1000);

// 可以使用箭头函数函数m 更简洁的语法
setTimeout(() => user.sayHi(), timeout);
```



上述的方法有一个问题就是函数执行有一秒的延迟，但是在这一秒之内，原来的对象被修改了，name将会执行被修改后的对象的方法，或者直接抛出异常。

## 3 解决方案二：bind

函数提供了一个内建方法，他可以绑定this，基本的语法为：

```js
let boundFunc = func.bind(context);
```

简单来说就是把`this`，提供给`func`，和之前的`func.call(contenxt, arg1,...)`和`func.apply(contenxt, ...args)`类似。

```js
let user = {
    firstName: "John"
};

function func() {
    console.log(this.firstName);
}

let funcUser = func.bind(user);
funcUser(); // John
```



现在来尝试解决解决方案一出现的问题。

```js
let user = {
    firstName: "John",
    sayHi(phrase) {
        console.log(`${phrase}, ${this.firstName}!`);
    }
};

let sayHi = user.sayHi.bind(user); // (*)


// 可以在没有对象（译注：与对象分离）的情况下运行它
sayHi("李四"); // 李四, John!

setTimeout(sayHi, 1000, "张三"); // 张三, John!

// 即使 user 的值在不到 1 秒内发生了改变
// sayHi 还是会使用预先绑定（pre-bound）的值，该值是对旧的 user 对象的引用
```



现在使用`bind`来操作函数，并且函数能够保持在对象改变的情况下，保持对原来对象的引用。同样可以正常运行。`let sayHi = user.sayHi.bind(user);` 我们将`user`对象的方法绑定给`sayHi`，他可以被单独调用，也可以传递到`setTimeout`中。



如果一个对象有很多方法， 可以在循环中进行绑定。

```js
for (let key in user) {
    if (typeof user[key] == 'function') {
        user[key] = user[key].bind(user);
    }
}
```



## 4 部分应用函数 （Partial functions）

JS中不仅可以绑定函数，还可以绑定参数，在python中也有同样的概念，Python中partial的基本使用：

```python
from functools import partial

def sum(a, b):
    return a + b

sum_1 = partial(sum, 10)


if __name__ == "__main__":
    print(sum_1(2)) 
```



JS partial的基本语法：`let bound = func.bind(context, [arg1], [arg2], ...);`他可以将上下文绑定为`this`，且绑定部分参数。例如：

```js
function mul(a, b) {
    return a * b;
}

let double = mul.bind(null, 2);

console.log(double(3)); // = mul(2, 3) = 6
console.log(double(4)); // = mul(2, 4) = 8
console.log(double(5)); // = mul(2, 5) = 10
```



值得一提的是，当没有上下文`this`需要绑定的时候， 要显示的将上下文设置为`null`。或者可以理解为将`null`绑定为了上下文。但是不能省略。

可用的场景大概是：①：我们再原来的函数的基础上创建一个可独享较高的独立函数，且不必每次都提供一个参数，因为参数是被绑定了的。②：或者是当我们有一个很灵活的函数，希望有一个不那么领灵活的变形时，可以使用这个方法。





## 5 没有上下文的部分应用函数 （Partial functions）



当我们只想为一些函数绑定参数，但是没有上下文，原生的`bind`不支持这样。这是我们可以这么做：

```js
function partial(func, ...argsBound) {
    return function (...args) {
        return func.call(this, ...argsBound, ...args);
    }
}
```

`function partial(func, ...argsBound)`调用的结果是一个包装器，这个包装器调用`func`，并且与他获得的函数具有相同的`this`，`...argsBound`是`function partial(func, ...argsBound)`的参数，`...args`是给包装器的参数。例如：

```js
let user = {
  firstName: "John",
  say(time, phrase) {
    alert(`[${time}] ${this.firstName}: ${phrase}!`);
  }
};

// 添加一个带有绑定时间的 partial 方法
// new Date().getHours() + ':' + new Date().getMinutes() 是包装器的参数，就是...argsBound
user.sayNow = partial(user.say, new Date().getHours() + ':' + new Date().getMinutes());

// Hello 是包装器的参数。
user.sayNow("Hello");

// 输出的结果： [22:41] John: Hello!
```



## 6 总结

- 方法`func.bind(context, ...args)`返回函数`func`的“绑定变体”，它绑定了上下文 this 和第一个参数（如果给定了）。我们一般使用`bind`绑定`this`传递到其他地方用，例如传递给`setTimeout`。
- 当绑定现有函数的一些参数后，绑定的函数被称为 partially  applied或者偏函数（自创）。
- bind 绑定的结果是一个新的对象，他没有原来函数的属性。
- 一个函数不能被重绑定（re-bound）。











































