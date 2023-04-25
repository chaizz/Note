---
title: JS笔记之JS类(三)
author: chaizz
date: 2023-3-10
tags: JavaScript
photo: ["https://origin.chaizz.com/ec55444c4a1211edac740242ac190002.png"]
---

​         

<!--more-->

# JS笔记之JS类(三)

## 1 静态属性和方法

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

或者使用静态类创建一个工厂方法：

```js
class Article {
    constructor(title, date) {
        this.title = title;
        this.date = date;
    }

    static createTodays() {
        // 记住 this = Article
        return new this("Today's digest", new Date());
    }
}

let article = Article.createTodays();

console.log(article.title); // Today's digest
```

静态方法也被用于与数据库相关的公共类，可以用于搜索/保存/删除数据库中的条目， 就像这样：

```js
// 假定 Article 是一个用来管理文章的特殊类
// 通过 id 来移除文章的静态方法：
Article.remove({id: 12345});
```

> 静态方法不是类的对象的方法，二是类本身的方法。
>
> 他可以再类上调用， 但是不能在对象上调用。



## 2 静态属性

JS比较新的特性。他看起来就像常规的类的属性，但是前面回家一个 static ：

```js
class Article {
  static publisher = "Levi Ding";
}

alert( Article.publisher ); // Levi Ding
```

这种定义的方式， 等同于直接给Article赋值。`Article.publisher = "Levi Ding";`


继承静态属性和方法

类的静态属性和方法同样可以继承，也是通过原型链进行查找。extends 让 Rabbit 的 [[Prototype]] 指向了 Animal。

![image-20230313151407307](https://origin.chaizz.com/tc/image-20230313151407307.png)



所以，`Rabbit extends Animal` 创建了两个 `[[Prototype]]` 引用：

1. `Rabbit` 函数原型继承自 `Animal` 函数。
2. `Rabbit.prototype` 原型继承自 `Animal.prototype`



## 3 总结

原型的静态方法被应用于整个类的功能， 和具体的实例没有关系，在类声明中，它们都被用关键字 static 进行了标记。

静态属性被用于当我们想要存储类级别的数据时，而不是绑定到实例。

```js
class MyClass {
    static property = ...;

    static method() {
      ...
    }
}
```

从技术上讲，静态声明与直接给类相同。

```js
MyClass.property = ...
MyClass.method = ...
```

静态属性和方法是可被继承的。

对于 `class B extends A`，类 `B` 的 prototype 指向了 `A`：`B.[[Prototype]] = A`。因此，如果一个字段在 `B` 中没有找到，会继续在 `A` 中查找。
