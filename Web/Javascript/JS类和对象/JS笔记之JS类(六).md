---
title: JS笔记之JS类(六)
author: chaizz
date: 2023-3-18
tags: JavaScript
photo: ["https://origin.chaizz.com/ec55444c4a1211edac740242ac190002.png"]
---

​         

<!--more-->

# JS笔记之JS类(六)



在JS中类只能扩展一个类， 每个对象只能有一个[[prototype]],但是有些时候，会让人感到限制，例如我们有一个User类和一个EventEmitter类来实现事件生成，并且想将EventEmitter的功能添加到User类中，以便用户可以触发事件。

有一个概念可以帮我们， 叫做 mixins， 在维基百科中 mixin是一个可以被其他的类使用，而无需继承的方法的类。

换句话说就是 mixin 提供了一些特殊的方法，但是我们不单独使用它，而是用它来将这些行为添加到其他的类中，

## 1 一个Mixin实例

在JS中构造一个mixin最简单的方法就是构造一个拥有使用方法的对象，以便我们可以轻松的将这些实用的方法合并到任何类的原型中。例如这个名为sayHiMixin的mixin用于给User添加一些语言功能。

```js
// mixin
let sayHiMixin = {
    sayHi() {
        console(`Hello ${this.name}`);
    },
    sayBye() {
        console(`Bye ${this.name}`);
    }
};

// 用法：
class User {
    constructor(name) {
        this.name = name;
    }
}

// 拷贝方法
Object.assign(User.prototype, sayHiMixin);

// 现在 User 可以打招呼了
new User("Dude").sayHi(); // Hello Dude!
```

在这里没有使用继承，而只是一个简单的拷贝，所以User可以从另一个类继承，还可以包括mixin来"mix-in"其他的方法，例如：

```js
class User extends Person {
    // ...
}
Object.assign(User.prototype, sayHiMixin);
```

Mixin可以在自己的内部进行继承，例如：

```js
let sayMixin = {
    say(phrase) {
        console(phrase);
    }
};

let sayHiMixin = {
    __proto__: sayMixin, // (或者，我们可以在这儿使用 Object.setPrototypeOf 来设置原型)

    sayHi() {
        // 调用父类方法
        super.say(`Hello ${this.name}`); // (*)
    },
    sayBye() {
        super.say(`Bye ${this.name}`); // (*)
    }
};

class User {
    constructor(name) {
        this.name = name;
    }
}

// 拷贝方法
Object.assign(User.prototype, sayHiMixin);

// 现在 User 可以打招呼了
new User("Dude").sayHi(); // Hello Dude!
```



在sayHiMixin内部对父类的方法，super.say()的调用会在mixin的原型中查找方法，而不是在class中查找。

![image-20230318164932986](https://origin.chaizz.com/tc/image-20230318164932986.png)





这是因为方法sayHi和sayBye最初是在sayHiMixin中创建的，因此即使复制了他们，但是他们的[[homeObject]]内部属性扔引用的sayHiMixin。

当 super 在 [[HomeObject]].[[Prototype]] 中寻找父方法时，意味着它搜索的是 sayHiMixin.[[Prototype]]，而不是 User.[[Prototype]]。



## 2 EventMixin

许多浏览器对象的一个重要功能就是可以生成事件，事件是向任何有需要的人“广播信息”的好方法。

我们将构造一个对象，能够轻松的将与事件相关的函数添加到任意的calss或者是object中。

1. Mixin提供.trigger(name,[...data])方法，以在发生重要的事情时，生成一个事件。name参数（arguments）是事件的名称，[...data] 是可选的带有事件数据的其他参数。
2. 此外还有.on(name,handler)方法，他为具有给定名称的事件添加了handler函数作为监听器，（listener）当具有给定name的事件触发时将调用该方法，并从.trigger调用中参数（arguments）。
3. 还有.off(name, handler)方法，他会删除handler监听器（listener）。

田家湾mixin后，对象user能够在访客登录时，生成事件login, 另一个对象，例如calender可能希望监听此类事件以便为登录的人添加日历。

或者当一个菜单选项被选中时，menu可以生成select事件，其他对象可以分配处理程序以对该事件作出反应。诸如此类。

例如：

```js
let eventMixin = {
    /**
     * 订阅事件，用法：
     *  menu.on('select', function(item) { ... }
    */
    on(eventName, handler) {
        if (!this._eventHandlers) this._eventHandlers = {};
        if (!this._eventHandlers[eventName]) {
            this._eventHandlers[eventName] = [];
        }
        this._eventHandlers[eventName].push(handler);
    },

    /**
     * 取消订阅，用法：
     *  menu.off('select', handler)
     */
    off(eventName, handler) {
        let handlers = this._eventHandlers?.[eventName];
        if (!handlers) return;
        for (let i = 0; i < handlers.length; i++) {
            if (handlers[i] === handler) {
                handlers.splice(i--, 1);
            }
        }
    },

    /**
     * 生成具有给定名称和数据的事件
     *  this.trigger('select', data1, data2);
     */
    trigger(eventName, ...args) {
        if (!this._eventHandlers?.[eventName]) {
            return; // 该事件名称没有对应的事件处理程序（handler）
        }

        // 调用事件处理程序（handler）
        this._eventHandlers[eventName].forEach(handler => handler.apply(this, args));
    }
};
```

用法：

```js
// 创建一个 class
class Menu {
    choose(value) {
        this.trigger("select", value);
    }
}
// 添加带有事件相关方法的 mixin
Object.assign(Menu.prototype, eventMixin);

let menu = new Menu();

// 添加一个事件处理程序（handler），在被选择时被调用：
menu.on("select", value => console.log(`Value selected: ${value}`));

// 触发事件 => 运行上述的事件处理程序（handler）并显示：
// 被选中的值：123
menu.choose("123");
```





## 3 总结

Mixin是一个通用的面向对象的变成术语，一个包含其他类的方法的类。

JS不支持多继承，但是可以通过将方法拷贝到原型中来实现，mixin。我们可以使用mixin作为一种通过添加多种行为来扩充类的方法。

如果 Mixins 意外覆盖了现有类的方法，那么它们可能会成为一个冲突点。因此，通常应该仔细考虑 mixin 的命名方法，以最大程度地降低发生这种冲突的可能性。

























