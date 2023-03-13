---
title: JS笔记之JS类(一)
author: chaizz
date: 2023-3-08
tags: JavaScript
photo: ["https://tc.chaizz.com/ec55444c4a1211edac740242ac190002.png"]
---

​         

<!--more-->

# JS笔记之JS类(一)

在日常的开发中，京城需要构建许多类型相同的对象，例如 Users, Goods ，我们可以使用构造器来完成这样的操作，但是在现代JS中可以是使用类这种方式来创建相同类型的对象。

## 1 类基本语法

```js
class MyClass {
    // class 方法
    constructor() { ... }
    method1() { ... }
    method2() { ... }
    method3() { ... }
    ...
}
    
let user = new User()
```

new 会自动调用`constructor`方法，因此可以在`constructor`方法中初始化对象。

```js
class User {

    constructor(name) {
        this.name = name;
    }

    sayHi() {
        alert(this.name);
    }

}

// 用法：
let user = new User("John");
user.sayHi();
```

在python中创建类的方式也大同小异。

```python
class User:

    def __init__(self, name):
        self.name = name

    def say_hi(self):
        print(f"我的名字是：{self.name}，Hi~")


if __name__ == '__main__':
    user = User('张三')
    user.say_hi()
```

在JS中当 new User("john")被调用时，代表一个新的对象被创建，constructor 使用给定的参数运行，并且将其赋值给this.name。

> Tips：
>
> 类的方法之间没有逗号。



## 2 所以class到底是什么？

在JS中类是一种函数，class User(...) 实际上做了如下的事情：

1. 创建一个名为User的函数，该函数称为类声明的结果，该函数的代码来自于constructor方法（如果不编写这个方法，那么就为空）。
2. 存储类中的方法，例如 User.prototype.sayHi



## 3 class 不仅仅是语法糖

声明上述的User对象不使用类也可以声明一个同样的对象：`

```js
// 用纯函数重写 class User

// 1. 创建构造器函数
function User(name) {
    this.name = name;
}
// 函数的原型（prototype）默认具有 "constructor" 属性，
// 所以，我们不需要创建它

// 2. 将方法添加到原型
User.prototype.sayHi = function () {
    alert(this.name);
};

// 用法：
let user = new User("John");
user.sayHi();
```

虽然得到的结果基本相同，但是他们之间仍然存着巨大的差异。

1. 通过class创建的函数具有特殊的内部属性标记。[[IsClassConstructor]]:true。

   必须使用 new 来创建它。

   ```js
   class User {
       constructor() { }
   }
   
   console.log(typeof User); // function
   User();  // Class constructor User cannot be invoked without 'new'
   ```

2. 类方法不可枚举，类定义将 prototype 中的所有的方法的描述符enumerable设置为fasle。

3. 类总是使用 user strict , 在类构造中的代码都自动进入严格模式。



## 4 类表达式

类会像函数一样可以在另外一个表达式中被定义，被传递，返回，赋值等。如果类表达式有名字，那么该名字仅在类内部可见。且可以动态的创建类。

```js

let User = class {
    sayHi() {
        alert("Hello");
    }
};


// “命名类表达式（Named Class Expression）”
// (规范中没有这样的术语，但是它和命名函数表达式类似)
let User = class MyClass {
    sayHi() {
        alert(MyClass); // MyClass 这个名字仅在类内部可见
    }
};

new User().sayHi(); // 正常运行，显示 MyClass 中定义的内容

alert(MyClass); // error，MyClass 在外部不可见


// 动态的创建类
function makeClass(phrase) {
    // 声明一个类并返回它
    return class {
        sayHi() {
            alert(phrase);
        }
    };
}

// 创建一个新的类
let User = makeClass("Hello");

new User().sayHi(); // Hello

```



## 5 getters/setters

类同样可能包括getters/setters，以及计算属性 （computed properties）等。

```js
class User {

    constructor(name) {
        // 调用 setter
        this.name = name;
    }

    get name() {
        return this._name;
    }

    set name(value) {
        if (value.length < 4) {
            alert("Name is too short.");
            return;
        }
        this._name = value;
    }

}

let user = new User("John");
alert(user.name); // John

user = new User(""); // Name is too short.
```



## 6 计算属性

计算属性的方法名称使用中括号声明：

```js
class User {

    ['say' + 'Hi']() {
        console.log("Hello");
    }

}

new User().sayHi();
```



## 7 类字段

类字段是一种允许添加任何属性的语法，类字段的重要的不同之处在于他们会在每个对立对象中被设置好，而不是在User.prototype：

```js
class User {
    name = "John";
}

let user = new User();
console.log(user.name); // John
console.log(User.prototype.name); // undefined
```



## 8 使用类字段实现绑定方法

在JS中具有动态的this，它取决于调用上下文，如果一个对象方法被传递到某处，或者在另一个上下文中被调用，则this不在是起对象的引用。例如：

```js
class Button {
    constructor(value) {
        this.value = value;
    }

    click() {
        console.log(this.value);
    }
}

let button = new Button("hello");

setTimeout(button.click, 1000); // undefined
```



在函数进阶中的函数绑定中说明过这个情况，解决的方法有两种，一是使用包装器，二是将方法绑定到对象。

```js
// 第二种方法
class Button {
    constructor(value) {
        this.value = value;
    }
    // 他是基于每一个对象创建的，在这里每一个button都有一个独立的方法，内部都有一个指向此对象的this,可以把button.click()传递到任何都一个对象。
    click = () => {
        console.log(this.value);
    }
}

let button = new Button("hello");

setTimeout(button.click, 1000); // hello

```

> 在浏览器环境中，它对于进行事件监听尤为有用。



## 9 总结

类的基本语法

```js
class MyClass {
    prop = value; // 属性

    constructor(...) { // 构造器
        // ...
    }

    method(...) { } // method

    get something(...) { } // getter 方法
    set something(...) { } // setter 方法

    [Symbol.iterator]() { } // 有计算名称（computed name）的方法（此处为 symbol）
    // ...
}
```

技术上来说，`MyClass` 是一个函数（我们提供作为 `constructor` 的那个），而 methods、getters 和 setters 都被写入了 `MyClass.prototype`。





