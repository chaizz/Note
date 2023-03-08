---
title: JS笔记之JS对象基础知识(五)
author: chaizz
date: 2023-1-17 17:24:16
tags: JavaScript
photo: ["https://tc.chaizz.com/ec55444c4a1211edac740242ac190002.png"]
---

​          

<!--more-->

# JS笔记之JS对象基础知识(五)

## 1 构造器和操作符new

创建对象可以使用字面量的方式：{} 也可以使用new操作符，相较于字面量创建对象的方式，new操作符能够方面的创建许多类似的对象，例如多个用户和菜单对象。

### 1.1 构造函数

构造函数在技术上是常规函数，只是有两个约定：

- 他的命名都是一大写字母开头。
- 只能有`new`操作符来执行。

例如：

```js
function User(name) {
  this.name = name;
  this.isAdmin = false;
}

let user = new User("Jack");

alert(user.name); // Jack
alert(user.isAdmin); // false
```

### 1.2 构造器的执行步骤：

1. 一个新的空对象被创建并分配给this。
2. 函数体执行，通常他会修改`this`,并为其添加新的属性。
3. 返回`this`的值。

总的来说 new User(...) 做的就是如下的事情：

```js
function User(name) {
  // this = {};（隐式创建）

  // 添加属性到 this
  this.name = name;
  this.isAdmin = false;

  // return this;（隐式返回）
}
```



```js
let user1 = new User("Jack");

let user2 = {
    name:'Jack',
    isAdmin:false,
}
// 所以 user1 和 user2 的结果是相通的对象。
```

所以接下来我们创建新的对象，就可以使用 `new User('Alice')` `new User('Ann')`等， 要比每次都是用字面量要更与阅读。

**这就是构造起的主要目的：实可重用的对象创建代码。**

从技术上将，除了箭头函数以外，任何函数都可以用作构造器，即可以通过`new`来运行，他会执行上面的构造器的执行步骤。“首字母大写”是一个共同的约定，以明确表示一个函数将被使用 `new` 来运行。

封装构建单个对象的代码的技巧：

如果有许多行用于创建单个复杂对象的代码，我们可以将它们封装在一个立即调用的构造函数中，像这样：

```js
// 创建一个函数并立即使用 new 调用它
let user = new function() {
  this.name = "John";
  this.isAdmin = false;

  // ……用于用户创建的其他代码
  // 也许是复杂的逻辑和语句
  // 局部变量等
};
```

这个构造函数不能被再次调用，因为他不保存在任何地方，只是被创建和调用。

### 1.3 构造器模式测试：new.target

在一个函数内部我们是可以使用new.target 属性来检查他是否被使用new调用了。对于常规调用，它是 undefined, 但是对于new的调用，则等于该函数。

```js
function User() {
  alert(new.target);
}

// 不带 "new"：
User(); // undefined

// 带 "new"：
new User(); // function User { ... }
```

主要的使用方式：被使用在函数的内部，来判断该函数是被通过new调用的构造器模式，还是常规模式；也可以让new调用和常规调用最相同的工作。

```js
function User(name) {
  if (!new.target) { // 如果你没有通过 new 运行我
    return new User(name); // ……我会给你添加 new
  }

  this.name = name;
}

let john = User("John"); // 将调用重定向到新用户
alert(john.name); // John
```



### 1.4 构造起的return

通常构造器没有return，他们的任务是将所有必须要写入的东西写入到this，并自动转化为结果，但是如果有return语句就会有不同的规则：

- 如果返回的是一个对象，怎返回这个对象，不返回this。
- 如果返回的是一个原始类型，则忽略。

换句话说，带有对象的 `return` 返回该对象，在所有其他情况下返回 `this`。例如：

```js
function BigUser() {

  this.name = "John";

  return { name: "Godzilla" };  // <-- 返回这个对象
}

alert( new BigUser().name );  // Godzilla，得到了那个对象
```

这里有一个 `return` 为空的例子（或者我们可以在它之后放置一个原始类型，没有什么影响）

```js
function SmallUser() {

  this.name = "John";

  return; // <-- 返回 this
}

alert( new SmallUser().name );  // John
```

Tips：构造函数没有参数，可以在被new调用的时候可以省略参数。（这里省略括号不被认为是一种“好风格”，但是规范允许使用该语法。）

```js
let user = new User; // <-- 没有参数
// 等同于
let user = new User();
```

使用构造函数来创建对象会带来很大的灵活性。构造函数可以传入一些参数，又或者是一些方法。使创建的对象更加灵活。



### 1.5 总结

- 构造函数或者简称构造器，就是常规函数，只不过对他有着共同的约定，就是首字母大写。
- 构造函数只能使用`new`来调用，这样就意味着在创建了一个空的对象`this`, 并且最后返回了这个`this`。