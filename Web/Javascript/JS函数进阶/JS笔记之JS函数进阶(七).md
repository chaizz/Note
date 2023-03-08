---
title: JS笔记之JS函数进阶(七)
author: chaizz
date: 2023-3-03
tags: JavaScript
photo: ["https://tc.chaizz.com/ec55444c4a1211edac740242ac190002.png"]
---

​         

<!--more-->

# JS笔记之JS函数进阶(七)



## 1 箭头函数

箭头函数不是简单的函数的简写的形式， 它是独特的函数，适合在使用"小函数"的场景下使用。例如：

- arra.forEach(func) 给数组的每个参数都执行func。

- setTimeout(func) 使用定时器执行函数。

## 2 箭头函数的特性：

1. 没有this
2. 没有arguments
3. 不能使用new构造器来定义箭头函数。
4. 箭头函数和 `func.bind(this)` 的区别：`func.bind(this)`创建了一个`func`的绑定版本，箭头函数没有创建任何绑定，只是没有this，this的查找方式，和普通的函数一致，在外部词法环境中查找。





## 3 使用场景1

利用箭头函数没有this的特性，在对象中定义的方法中使用箭头函数来获取对象的属性。

```js
let group = {
    title: "Our Group",
    students: ["John", "Pete", "Alice"],

    showList() {
        this.students.forEach(
            // 此处箭头函数没有this，就会获取上级group对象的this
            student => console.log(this.title + ': ' + student)
        );
    }
    /* 使用正常的函数， 此时正常函数的this 是 undefined，
    showList() {
        this.students.forEach(function (student) {
            // Error: Cannot read property 'title' of undefined
            console.log(this.title + ': ' + student);
        });
    }
    */
};
```



## 4 使用场景2

```js
function defer(f, ms) {
    return function () {
        // 在定时器中使用箭头函数，不需要关注上下文和 arguments
        setTimeout(() => f.apply(this, arguments), ms);
    };
}



function defer(f, ms) {
    return function (...args) {
        // 不使用箭头函数的写法
        let ctx = this;
        setTimeout(function () {
            return f.apply(ctx, args);
        }, ms);
    };
}

function sayHi(who) {
    console.log('Hello, ' + who);
}

let sayHiDeferred = defer(sayHi, 2000);
sayHiDeferred("John"); // 2 秒后显示：Hello, John
```



**箭头函数是针对那些没有自己的“上下文”，但在当前上下文中起作用的短代码的。**

