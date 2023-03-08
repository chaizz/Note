---
title: JS笔记之JS对象知识扩展之对象属性
author: chaizz
date: 2023-3-03
tags: JavaScript
photo: ["https://tc.chaizz.com/ec55444c4a1211edac740242ac190002.png"]
---

​         

<!--more-->

# JS笔记之JS对象知识扩展之对象属性



在JS对象基础知识中，我们属性了对象的一些属性，但都是一些简单的键值对。

对象属性除了基本的value之外还会有三个特殊的属性，也叫做属性标志：

- writable 如果为true，则值可以被修改，斗则他是只读的。
- enumerable 如果为true，则会被循环中列出，否则不会被列出。
- configurable 如果为true，则此属性可以被删除，这些属性也可以被修改，否则不可以。

当我们在对象中用常用的的方式，创建一个属性时，**他们默认都为true**，但是也可随时更改他们。

## 1 首先如何获取这些值呢？ 

```js
// obj 需要从中获取信息的对象。
// propertyName：属性的名称
let descriptor = Object.getOwnPropertyDescriptor(obj, propertyName);
```

返回的`descriptor`是一个“属性描述符”对象，它包含值和所有的属性标志。

```js
let user = {
    name: "John"
};

let descriptor = Object.getOwnPropertyDescriptor(user, 'name');

console.log(JSON.stringify(descriptor, null, 4));

// 结果
/* {
    "value": "John",
    "writable": true,
    "enumerable": true,
    "configurable": true
} */
```

## 2 如何修改这些值呢？

```js
// obj 对象。
// propertyName：属性的名称
// descriptor要应用的属性描述符对象。
Object.defineProperty(obj, propertyName, descriptor)
```

> 如果该属性存在，`defineProperty` 会更新其标志。否则，它会使用给定的值和标志创建属性；在这种情况下，如果没有提供标志，则会假定它是 `false`。

```js
let user = {};

Object.defineProperty(user, "name", {
    value: "John"
});

let descriptor = Object.getOwnPropertyDescriptor(user, 'name');

console.log(JSON.stringify(descriptor, null, 4));

// 结果
/* {
    "value": "John",
    "writable": false,
    "enumerable": false,
    "configurable": false
} */


// 与常用的方式创建对象，进行比较， 这些个属性标志都是false。
```

### 2.1 把某个属性设为只读

```js
let user = {
    name: "John"
};

Object.defineProperty(user, "name", {
    writable: false
});
// 重新给user.name赋值， 此处并没有抛出异常，没有任何变动。 （咱严格模式下，会抛出异常）
user.name = "Pete";
// 输出user.name 还是原来的 名字， 说明上述的赋值没有成功。
console.log(user.name);
```

如果创建新的属性，需要指明那些属性为true。

```js
let user = {};

Object.defineProperty(user, "name", {
    value: "John",
    // 对于新属性，我们需要明确地列出哪些是 true
    enumerable: true,
    configurable: true
});

console.log(user.name); // John
user.name = "Pete"; 
```

### 2.2 把某个属性设为不可枚举

通常对象的toString都是不可枚举的，他不会显示在for...in中，但是我盟添加自己的toString，默认情况下它会显示在for...in中。

```js
let user = {
    name: "John",
    toString() {
        return this.name;
    }
};

// 默认情况下，我们的两个属性都会被列出：
for (let key in user) console.log(key); // name, toString
```

我们可以设置`toString`的属性标志`enumerable`为`false`，将他设置为不可枚举。

```js
Object.defineProperty(user, "toString", {
    enumerable: false
});

// 现在我们的 toString 消失了：
for (let key in user) console.log(key); // name
```

### 2.3 把属性设置为不可配置（即不能被修改也不能被删除）

不可配置标志（`configurable:false`）有时会预设在内建对象和属性中。

不可配置的属性不能被删除，它的特性（attribute）不能被修改。

```js
let descriptor = Object.getOwnPropertyDescriptor(Math, 'PI');
console.log(JSON.stringify(descriptor, null, 4));
// 此处 Math.PI 是只读的、不可枚举和不可配置的：使属性变成不可配置是一条单行道。我们无法通过 defineProperty 再把它改回来。

// {
//     "value": 3.141592653589793,
//     "writable": false,
//     "enumerable": false,
//     "configurable": false
// }
```

**请注意：`configurable: false` 防止更改和删除属性标志，但是允许更改对象的值。**

> **唯一可行的特性更改：writable true → false**
>
> 对于更改标志，有一个小例外。
>
> 对于不可配置的属性，我们可以将 `writable: true` 更改为 `false`，从而防止其值被修改（以添加另一层保护）。但无法反向行之。



## 3 一次定义多个属性

要获取所有的描述符，可以使用`Object.getOwnPropertyDescriptors`，他与`Object.defineProperty` 可以一起用作克隆对象的标志感知标志。

```js
let clone = Object.defineProperties({}, Object.getOwnPropertyDescriptors(obj));
```

通常，当我们克隆一个对象的时候，我们是用赋值的方式来赋值属性。例如：

```js
for (let key in user) {
  clone[key] = user[key]
}
```

但是像上面这样并不能复制对象的属性标志，如果要原对象的属性标志，可以使用`Object.defineProperties`，另一个区别是 `for..in` 会忽略 symbol 类型的和不可枚举的属性，但是 `Object.getOwnPropertyDescriptors` 返回包含 symbol 类型的和不可枚举的属性在内的 **所有** 属性描述符。



## 4 设定一个全局的密封对象

属性描述符是作用在单个属性上工作，还有一些限制访问整个对象的方法：

- Object.preventExtensions(obj) 禁止向对象添加新属性。
- Object.seal(obj) 禁止添加/删除属性。为所有现有的属性设置 configurable: false。
- Object.freeze(obj) 禁止添加/删除/更改属性。为所有现有的属性设置 configurable: false, writable: false。

测试相关：

- Object.isExtensible(obj) 如果添加属性被禁止，则返回 false，否则返回 true。
- Object.isSealed(obj) 如果添加/删除属性被禁止，并且所有现有的属性都具有 configurable: false则返回 true。
- Object.isFrozen(obj) 如果添加/删除/更改属性被禁止，并且所有当前属性都是 configurable: false, writable: false，则返回 true。

这些方法在实际中很少使用。



## 5 属性的getter和setter 



对象属性有两种类型，一种是对象属性，就是常规的属性。一种是访问器属性，他的本质是用于获取和设置值的函数。

访问器属性由`getter`和`setter`方法表示，在对象字面量中使用get和set表示：

```js
let obj = {
    get propName() {
        // 当读取 obj.propName 时，getter 起作用
    },

    set propName(value) {
        // 当执行 obj.propName = value 操作时，setter 起作用
    }
};
```

我们有一个具有 `name` 和 `surname` 属性的对象 `user`：

```js
let user = {
    name: "John",
    surname: "Smith",
    // 使用访问器，实行 user.fullname
    get fullName() {
        return `${this.name} ${this.surname}`;
    }
};

console.log(user.fullName); // John Smith
```

从外表看，访问器属性看起来就像一个普通属性。这就是访问器属性的设计思想。我们不以函数的方式 **调用** `user.fullName`，我们正常 **读取** 它：getter 在幕后运行。

截至目前，`fullName` 只有一个 getter。如果我们尝试赋值操作 `user.fullName=`，将会出现错误。

使用`setter`来设置 `user.fullname`:

```js
let user = {
    name: "John",
    surname: "Smith",

    get fullName() {
        return `${this.name} ${this.surname}`;
    },

    set fullName(value) {
        [this.name, this.surname] = value.split(" ");
    }
};

// set fullName 将以给定值执行
user.fullName = "Alice Cooper";

console.log(user.name); // Alice
console.log(user.surname); // Cooper
```

普通的数据属性具有上面的三个属性标志，但是访问器属性和普通的数据属性不同。

对于访问器属性他没有 `value` 和 `writable`，但是有 `get` 和 `set` 函数。所以访问器的属性描述符由下列的属性标志:

- get 一个没有参数的函数。
- set 一个带有参数的函数。
- enumerable 和普通的数据属性相同，设置对象是否可在for...in中 枚举。
- configurable 是否可配置。

例如使用`defineProperty`创建一个`fullname`的访问器，可以使用get和set来传递描述符：

```js
let user = {
    name: "John",
    surname: "Smith"
};

Object.defineProperty(user, 'fullName', {
    get() {
        return `${this.name} ${this.surname}`;
    },

    set(value) {
        [this.name, this.surname] = value.split(" ");
    }
});

console.log(user.fullName); // John Smith

for (let key in user) console.log(key); // name, surname
```



> 对象中一个属性要么是访问器（具有 `get/set` 方法），要么是数据属性（具有 `value`），但不能两者都是。



## 6 更聪明的getter和setter

getter/setter 可以用作“真实”属性值的包装器，以便对它们进行更多的控制。

```js
// 如果我们想禁止太短的 user 的 name，我们可以创建一个 setter name，并将值存储在一个单独的属性 _name 中。

// 从技术上讲，外部代码可以使用 user._name 直接访问 name。但是，这儿有一个众所周知的约定，即以下划线 "_" 开头的属性是内部属性，不应该从对象外部进行访问。
let user = {
    get name() {
        return this._name;
    },

    set name(value) {
        if (value.length < 4) {
            console.log("Name is too short, need at least 4 characters");
            return;
        }
        this._name = value;
    }
};

user.name = "Pete";
console.log(user.name); // Pete

user.name = ""; // Name 太短了……
console.log(user.name);
```

访问器的一大用途是，它们允许随时通过使用 getter 和 setter 替换“正常的”数据属性，来控制和调整这些属性的行为。
