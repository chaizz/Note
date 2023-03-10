---
title: JS笔记之JS类(二)
author: chaizz
date: 2023-3-10
tags: JavaScript
photo: ["https://tc.chaizz.com/ec55444c4a1211edac740242ac190002.png"]
---

​         

<!--more-->

JS笔记之JS类(二)

类的继承, 使用“extends” 关键字

语法

```js
class Child extends Parent {
    someMethods(){
        console.log('继承');
    }
}
```

例字：

```js
// 构建一个动物类，有动物的一些方法属性，
class Animal {
    constructor(name) {
        this.speed = 0;
        this.name = name;
    }
    run(speed) {
        this.speed = speed;
        console.log(`${this.name} runs with speed ${this.speed}.`);
    }
    stop() {
        this.speed = 0;
        console.log(`${this.name} stands still.`);
    }
}

let animal = new Animal("My animal");


class Rabbit extends Animal {
    hide() {
        console.log(`${this.name} hides!`);
    }
}

let rabbit = new Rabbit("White Rabbit");

rabbit.run(5); // White Rabbit runs with speed 5.
rabbit.hide(); // White Rabbit hides!
```



在内部，关键字 extends 使用了原型的机制工作，他将`Rabbit.prototype.[[prototype]]`设置为了`Animal.prototype`，所以在Rabbit中找不到的方法，会自动去他的原型链上去寻找。

extends 后面可以根任意的表达式，例如：

```js
function f(phrase) {
    return class {
        sayHi() { alert(phrase); }
    };
}

class User extends f("Hello") { }

new User().sayHi(); // Hello
```

根据许多条件使用函数生成类，并继承它们时来说可能很有用。

重写类的方法

完全重新类的方法， 直接在子类中定义名称相同的方法名即可，

```js
class Rabbit extends Animal {
    stop() {
        // ……现在这个将会被用作 rabbit.stop()
        // 而不是来自于 class Animal 的 stop()
    }
}
```

但是我们通常都只是想扩展一下， 父类的方法。JS提供了 super 关键字。使用 super.method() 来调用一个父类。使用 super()一个父类的constructor。

```js
class Rabbit extends Animal {
    hide() {
        console.log(`${this.name} hides!`);
    }

    stop() {
        super.stop(); // 调用父类的 stop
        this.hide(); // 然后 hide
    }
}

let rabbit = new Rabbit("White Rabbit");

rabbit.run(5); // White Rabbit runs with speed 5.
rabbit.stop(); // White Rabbit stands still. White Rabbit hides!
```



> 箭头函数没有super，如果被访问他从外部获取，例如：
>
> ```js
> class Rabbit extends Animal {
>     stop() {
>         setTimeout(() => super.stop(), 1000); // 1 秒后调用父类的 stop
>     }
> }
> ```

重写 constructor

如果一个类继承了一个父类，但是没有重写constructor，那么他的constructor，默认是：

```js
class Rabbit extends Animal {
    // 为没有自己的 constructor 的扩展类生成的
    constructor(...args) {
        super(...args);
    }
}
```

现在我们设置自己子类的constructor，但是有一定的要求：

- 继承类的constructor， 必须调用 super()。
- 并且一定要在this 之前调用。

```js
class Rabbit extends Animal {
	
    constructor(name, earLength) {
        super(name);
        this.earLength = earLength;
    }

    // ...
}
```



重写类字段

一般错误的实现方法：

```js
class Animal {
    name = 'animal';

    constructor() {
        console.log(this.name); // (*)
    }
}

class Rabbit extends Animal {
    name = 'rabbit';
}

new Animal(); // animal
new Rabbit(); // animal
```

**是因为，父类构造器总是会使用它自己字段的值，而不是被重写的那一个。**

实际上，原因在于字段初始化的顺序。类字段是这样初始化的：

- 对于基类（还未继承任何东西的那种），在构造函数调用前初始化。
- 对于子类，在 super() 后立刻初始化

在上面的例子中，Rabbit是子类, 他没有实现constructor，所以是一个空的 super(...args)的构造器。

所以，new Rabbit() 调用了 super()，因此它执行了父类构造器，并且只有在此之后，它的类字段才被初始化。在父类构造器被执行的时候，Rabbit 还没有自己的类字段，这就是为什么 Animal 类字段被使用了。



总结

想要扩展一个类：`class Child extends Parent`。

这意味着 `Child.prototype.__proto__` 将是 `Parent.prototype`，所以方法会被继承。

重写一个 constructor：在使用 `this` 之前，我们必须在 `Child` 的 constructor 中将父 constructor 调用为 `super()`。

重写一个方法：我们可以在一个 `Child` 方法中使用 `super.method()` 来调用 `Parent` 方法。
