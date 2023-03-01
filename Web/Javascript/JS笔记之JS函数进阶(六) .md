---
title: JS笔记之JS函数进阶(六)
author: chaizz
date: 2023-3-01
tags: JavaScript
photo: ["https://tc.chaizz.com/ec55444c4a1211edac740242ac190002.png"]
---

​    

<!--more-->

JS笔记之JS函数进阶(六)





函数绑定

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

这是因为`settimeout` 获取到的函数 `user.sayHi` 和对象 `user` 分离了，无法在获取`user` 对象的上下文。

这是一种比较典型的问题：我们想将一个对象的方法，传递到别的地方调用，如何确保对象中的方法的上下文呢？

































