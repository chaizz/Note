---
title: JS笔记之JS类(三)
author: chaizz
date: 2023-3-10
tags: JavaScript
photo: ["https://tc.chaizz.com/ec55444c4a1211edac740242ac190002.png"]
---

​         

<!--more-->

JS笔记之JS类(三)

静态属性和方法

使用字面量创建一个 静态方法：

```js
class User {
    static staticMethod() {
        console.log(this === User);
    }
}
```

或者是我直接给类的属性复制一个方法：

```js
class User { }

User.staticMethod = function () {
    console.log(this === User);
};
```

通常，静态方法用于实现属于 整个类，但不属于该类任何特定对象的函数。

例如，我们创建一个article类，然后我们创建一个方法来比较这个article，通常的方案就是添加一个静态方法。

```js
class Article {
    constructor(title, date) {
        this.title = title;
        this.date = date;
    }

    static compare(articleA, articleB) {
        return articleA.date - articleB.date;
    }
}
// 用法：
```





















