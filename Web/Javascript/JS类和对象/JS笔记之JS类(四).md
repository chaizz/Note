---
title: JS笔记之JS类(四)
author: chaizz
date: 2023-3-13
tags: JavaScript
photo: ["https://tc.chaizz.com/ec55444c4a1211edac740242ac190002.png"]
---

​         

<!--more-->

JS笔记之JS类(四)

私有的和受保护的属性和方法

面向对象编程最重要的原则之一 —— 将内部接口与外部接口分隔开来。

在 JavaScript 中，有两种类型的对象字段（属性和方法）：

- 公共的：可从任何地方访问。它们构成了外部接口。到目前为止，我们只使用了公共的属性和方法。
- 私有的：只能从类的内部访问。这些用于内部接口。

首先创建一个咖啡机类:
```js
class CoffeeMachine {
    waterAmount = 0; // 内部的水量

    constructor(power) {
        this.power = power;
        alert(`Created a coffee-machine, power: ${power}`);
    }

}

// 创建咖啡机
let coffeeMachine = new CoffeeMachine(100);

// 加水
coffeeMachine.waterAmount = 200;
```

现在咖啡机的 waterAmount 和  power 是公共的属性，我们可以从外部轻易的修改，我们将 `waterAmount` 属性更改为受保护的属性，以对其进行更多控制。

受保护的属性

**受保护的属性通常以下划线 `_` 作为前缀。**这并不是强制的，但是是程序的约个约定俗称的。

```js
class CoffeeMachine {
    _waterAmount = 0;

    set waterAmount(value) {
        if (value < 0) {
            value = 0;
        }
        this._waterAmount = value;
    }

    get waterAmount() {
        return this._waterAmount;
    }

    constructor(power) {
        this._power = power;
    }

}

// 创建咖啡机
let coffeeMachine = new CoffeeMachine(100);

// 加水
coffeeMachine.waterAmount = -10; // _waterAmount 将变为 0，而不是 -10
```

现在访问已受到控制，因此将水量的值设置为小于零的数变得不可能。

设置只读属性，将咖啡机的 power属性设置为在开始创建之后就不可更改。我们值需要设施getter属性。

```js
class CoffeeMachine {
    // ...

    constructor(power) {
        this._power = power;
    }

    get power() {
        return this._power;
    }

}

// 创建咖啡机
let coffeeMachine = new CoffeeMachine(100);

alert(`Power is: ${coffeeMachine.power}W`); // 功率是：100W

coffeeMachine.power = 25; // Error（没有 setter）
```

在上述的代码中我们使用了 getter和setter语法，但是我们大多数使用get.../set... 这中函数：因为函数更灵活。它们可以接受多个参数，但是并没有严格约定使用那种方式。

```js
class CoffeeMachine {
    _waterAmount = 0;

    setWaterAmount(value) {
        if (value < 0) value = 0;
        this._waterAmount = value;
    }

    getWaterAmount() {
        return this._waterAmount;
    }
}

new CoffeeMachine().setWaterAmount(100);
```

> **受保护的字段是可以被继承的**
>
> 如果我们继承 `class MegaMachine extends CoffeeMachine`，那么什么都无法阻止我们从新的类中的方法访问 `this._waterAmount` 或 `this._power`。
>
> 所以受保护的字段是自然可被继承的。



私有的属性

**私有属性和方法应该以 `#` 开头。它们只在类的内部可被访问。**

```js
class CoffeeMachine {
    #waterLimit = 200;

    #fixWaterAmount(value) {
        if (value < 0) return 0;
        if (value > this.#waterLimit) return this.#waterLimit;
    }

    setWaterAmount(value) {
        this.#waterLimit = this.#fixWaterAmount(value);
    }

    print(){
        console.log(this.#waterLimit);
    }
}

let coffeeMachine = new CoffeeMachine();

// 不能从类的外部访问类的私有属性和方法
// Private field '#fixWaterAmount' must be declared in an enclosing class
coffeeMachine.#fixWaterAmount(123); // Error
coffeeMachine.#waterLimit = 1000; // Error
coffeeMachine.print()

```

在语言级别，`#` 是该字段为私有的特殊标志。我们无法从外部或从继承的类中访问它。

私有字段与公共字段不会发生冲突。我们可以同时拥有私有的 `#waterAmount` 和公共的 `waterAmount` 字段。例如 让waterAmount成为#waterAmount 的访问器。

```js
class CoffeeMachine {

    #waterAmount = 0;

    get waterAmount() {
        return this.#waterAmount;
    }

    set waterAmount(value) {
        if (value < 0) value = 0;
        this.#waterAmount = value;
    }
}

let machine = new CoffeeMachine();

machine.waterAmount = 100;
alert(machine.#waterAmount); // Error
```

与受保护的字段不同，私有字段由语言本身强制执行。当我们需要继承CoffeeMachine时， 私有字段不会被继承，需要依赖属性访问器`waterAmount` getter/setter。

许多种情况下，我们经常会扩展某个有私有属性的类，这种私有属性太限制，所以我们还是常常使用受保护的属性，即使它们不受语言语法的支持。

> **私有字段不能通过 this[name] 访问**
>
> 私有字段很特别。
>
> 正如我们所知道的，通常我们可以使用 `this[name]` 访问字段：
>
> ```javascript
> class User {
>   ...
>   sayHi() {
>     let fieldName = "name";
>     alert(`Hello, ${this[fieldName]}`);
>   }
> }
> ```
>
> 对于私有字段来说，这是不可能的：`this['#name']` 不起作用。这是确保私有性的语法限制。



扩展内建类

内建的 Array, Map 对象也是可以扩展的。例如我们扩展了一个内建的Array 对象：

```js
class PowerArray extends Array {
    isEmpty() {
        return this.length === 0;
    }
}

let arr = new PowerArray(1, 2, 5, 10, 50);
console.log(arr.isEmpty()); // false

let filteredArr = arr.filter(item => item >= 10);
console.log(filteredArr); // 10, 50
console.log(filteredArr.isEmpty()); // false
```



在上述代码中， 我们发现 PowerArray 的 arr 实例，具有Array的方法 filter, 由filter 得到的新数组具有PowerArray 的 isEmpty 方法。所以我们得知 arr.filter它内部使用的是arr.constructor， 来创建新的数组，因此我们才可以在结果数组上继续使用isEmpty方法。



我们可以给这个类添加一个特殊的静态访问器方法，Symbol.species，如果存在，则返回JS 在内部用来在 `map` 和 `filter` 等方法中创建新实体的 `constructor`。如果我们希望像 `map` 或 `filter` 这样的内建方法返回常规数组，我们可以在 `Symbol.species` 中返回 `Array`，就像这样：

```js
class PowerArray extends Array {
    isEmpty() {
        return this.length === 0;
    }

    // 内建方法将使用这个作为 constructor
    static get [Symbol.species]() {
        return Array;
    }
}

let arr = new PowerArray(1, 2, 5, 10, 50);
console.log(arr.isEmpty()); // false

// filter 使用 arr.constructor[Symbol.species] 作为 constructor 创建新数组
let filteredArr = arr.filter(item => item >= 10);

// filteredArr 不是 PowerArray，而是 Array
console.log(filteredArr.isEmpty()); // Error: filteredArr.isEmpty is not a function
```



>其他集合，例如 `Map` 和 `Set` 的工作方式类似。它们也使用 `Symbol.species`。



内建类没有静态方法继承

内建对象有它们自己的静态方法，例如 `Object.keys`，`Array.isArray` 等。

如我们所知道的，原生的类互相扩展。例如，`Array` 扩展自 `Object`。

通常，当一个类扩展另一个类时，静态方法和非静态方法都会被继承。这已经在 [静态属性和静态方法](https://zh.javascript.info/static-properties-methods#statics-and-inheritance) 中详细地解释过了。

但内建类却是一个例外。它们相互间不继承静态方法。



总结

就面向对象编程（OOP）而言，内部接口与外部接口的划分被称为 [封装](https://en.wikipedia.org/wiki/Encapsulation_(computer_programming))。

它具有以下优点：

- 保护用户，使他们不会误伤自己

  想象一下，有一群开发人员在使用一个咖啡机。这个咖啡机是由“最好的咖啡机”公司制造的，工作正常，但是保护罩被拿掉了。因此内部接口暴露了出来。所有的开发人员都是文明的 —— 他们按照预期使用咖啡机。但其中的一个人，约翰，他认为自己是最聪明的人，并对咖啡机的内部做了一些调整。然而，咖啡机两天后就坏了。这肯定不是约翰的错，而是那个取下保护罩并让约翰进行操作的人的错。编程也一样。如果一个 class 的使用者想要改变那些本不打算被从外部更改的东西 —— 后果是不可预测的。

- 可支持性

  编程的情况比现实生活中的咖啡机要复杂得多，因为我们不只是购买一次。我们还需要不断开发和改进代码。**如果我们严格界定内部接口，那么这个 class 的开发人员可以自由地更改其内部属性和方法，甚至无需通知用户。**如果你是这样的 class 的开发者，那么你会很高兴知道可以安全地重命名私有变量，可以更改甚至删除其参数，因为没有外部代码依赖于它们。对于用户来说，当新版本问世时，应用的内部可能被进行了全面检修，但如果外部接口相同，则仍然很容易升级。

- 隐藏复杂性

  人们喜欢使用简单的东西。至少从外部来看是这样。内部的东西则是另外一回事了。程序员也不例外。**当实施细节被隐藏，并提供了简单且有据可查的外部接口时，总是很方便的。**

为了隐藏内部接口，我们使用受保护的或私有的属性：

- 受保护的字段以 `_` 开头。这是一个众所周知的约定，不是在语言级别强制执行的。程序员应该只通过它的类和从它继承的类中访问以 `_` 开头的字段。
- 私有字段以 `#` 开头。JavaScript 确保我们只能从类的内部访问它们。

目前，各个浏览器对私有字段的支持不是很好，但可以用 polyfill 解决。











