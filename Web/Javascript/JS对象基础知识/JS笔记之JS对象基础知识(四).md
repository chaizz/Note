---
title: JS笔记之JS对象基础知识(四)
author: chaizz
date: 2023-1-17 16:19:46
tags: JavaScript
photo: ["https://origin.chaizz.com/ec55444c4a1211edac740242ac190002.png"]
---

​          

<!--more-->

# JS笔记之JS对象基础知识(四)

## 1 对象方法，this

通常创建对象来表示显示世界中的实体，比如用户或者订单。在现实世界中用户可以进行操作，比如登录注销，往购物车中添加商品。

在JS中 行为（action）由属性中的函数表示。

```js
let user = {
  name: "John",
  age: 30
};

user.sayHi = function() {
  alert("Hello!");
};

user.sayHi(); // Hello!
```

上面的例子中，我们给`user`对象的`sayHi`属性赋值了一个函数，这种对象的属性的函数被称为对象的方法。

**通常，全局中定义的函数称之为函数，对象中的属性是函数的话称之为方法。**

**我们在代码中使用对象表示实体时，这就是所谓的面向对象编程，简称OOP。**



### 1.1 方法简写

方法还有一个简写的方式 （可能会在继承层面会有些微的差别）

```js
// 这些对象作用一样

user = {
  sayHi: function() {		
    alert("Hello");
  }
};

// 方法简写看起来更好，对吧？
let user = {
  sayHi() { // 与 "sayHi: function(){...}" 一样
    alert("Hello");
  }
};
```



### 1.2 方法中的this

通常对象的方法可能需要访问对象的其他的属性，为了访问改对象，在方法中可以使用`this`这个关键字。`this`的值就是在点之前（**object**.attr）的这个对象，即调用该方法的对象。例如：

```js
let user = {
  name: "John",
  age: 30,

  sayHi() {
    // "this" 指的是“当前的对象”
    alert(this.name);
  }

};

user.sayHi(); // John
```

同样也可以使用这种方式访问到user的name属性：

```js
let user = {
  name: "John",
  age: 30,

  sayHi() {
    alert( user.name ); // 导致错误
  }

};
```

但是上述例子有一个明显的问题：如果给user重新赋值，那将访问到错误的对象。

```js
let admin = user;
user = null; // 重写让其更明显

admin.sayHi(); // TypeError: Cannot read property 'name' of null
```



### 1.3 this 不受限制

在JS中，`this`关键字可以用于任何函数，即使他不是对象的方法。 下面这样写也不会错误：

```js
function sayHi() {
  alert( this.name );
}
```

之所以不回错误，是因为`this`的值是计算出来的，它取决于代码的上下文。例如，这里相同的函数被分配给两个不同的对象，在调用中有着不同的 “this” 值：

```js
let user = { name: "John" };
let admin = { name: "Admin" };

function sayHi() {
  alert( this.name );
}

// 在两个对象中使用相同的函数
user.f = sayHi;
admin.f = sayHi;

// 这两个调用有不同的 this 值
// 函数内部的 "this" 是“点符号前面”的那个对象
user.f(); // John（this == user）
admin.f(); // Admin（this == admin）

admin['f'](); // Admin（使用点符号或方括号语法来访问这个方法，都没有关系。）
```

严格模式下，在没有对象的情况下调用：`this === undefined`，在非严格模式下，`this`是 全局对象，（浏览器中的Window）。通常这种调用是程序出错了。如果在一个函数内部有 `this`，那么通常意味着它是在对象上下文环境中被调用的

**在JS中`this`是自由的，他的值是实时计算出来的，他的值并不取决于方法声明的位置，而是取决于在“点符号之前”的是什么对象。**

**在运行时对`this`求值的这个概念优优点也有缺点，一方面函数可以被重用于不同的对象，另一方面更大的灵活性造成了更大的出错的可能性。**

箭头函数没有自己的this

箭头函数有些特别：它们没有自己的 `this`。这是箭头函数的一个特性，当我们并不想要一个独立的 `this`，反而想从外部上下文中获取时，它很有用。



### 1.4 总结：

- 存储在对象属性中的函数被称为方法。
- 方法允许对对像进行类似 obj.dosomething() 这样的操作。
- 方法可以将对象引用为`this`。
- `this`的值是在程序运行时得到的。
- 一个函数在运行时可能就定义了`this`，但是这个`this`只有在函数被调用时才会有值。
- 可以在对象之间复制函数。（不太懂）
- 以方法的语法调用函数时，`object.method()`，调用过程中的 `this` 值是 `object`。
- 箭头函数有些特别：它们没有 `this`。在箭头函数内部访问到的 `this` 都是从外部获取的。