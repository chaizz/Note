---
title: JS笔记之JS原型继承(一)
author: chaizz
date: 2023-3-06
tags: JavaScript
photo: ["https://origin.chaizz.com/ec55444c4a1211edac740242ac190002.png"]
---

​         

<!--more-->

# JS笔记之JS原型继承(一)



在JS中当我们需要在原有对象的基础上扩展一些额外的方法或者属性，就可以使用原型继承。

在JS中对象有一个隐藏属性：`[[Prototype]]`，他的值要么是一个对象的引用（此对象为原型）要么为`null`。

当我们从对象中读取一个缺失的的属性时，JS会自动从原型中回去该属性，这种特性被称为继。

## 1 设置 `[[Prototype]]`

使用特殊的名字`__proto__`。

```js
let animal = {
    eats: true
};
let rabbit = {
    jumps: true
};

rabbit.__proto__ = animal; // 设置 rabbit.[[Prototype]] = animal

console.log(rabbit.eats); // true
```

当获取`rabbit.eats`属性不存在时，JS会顺着`[[Prototype]]`引用在`animal`上找，自下而上。

原型链可以很长，但是他有两个限制。

1. 原型的引用不能形成闭环，试图给 `__proto__` 赋值但会导致引用形成闭环时，JavaScript 会抛出错误。
2. `__proto__`的值可以是对象，也可以是`null`，当时其他的数据类型时都会被忽略。
3. 一个对象只能有一个`[[Prototype]]`。

`__proto__`和`[[Prototype]]`

`__proto__`是`[[Prototype]]`的因历史原因而留下来的getter/setter。`__proto__`和`[[Prototype]]`的内部不一样。

`__proto__`是过时的属性，现代 JS建议我们使用函数`Object.getPrototypeO`和`Object.setPrototypeOf`来取代它。

根据规范`__proto__`必须仅受浏览器支持，但实际上服务端也支持，所以使用它还是很安全的。



## 2 写入时不使用原型

原型仅用于读取属性，在写入删除操作直接在对象上操作。

```js
let animal = {
    eats: true,
    walk() {
        /* rabbit 不会使用此方法 */
    }
};

let rabbit = {
    __proto__: animal
};

rabbit.walk = function () {
    console.log("Rabbit! Bounce-bounce!");
};

rabbit.walk(); // Rabbit! Bounce-bounce!
```

访问器属性是一个特使的例外：因为赋值操作是由setter处理，因此此类属性实际上与调用函数相同。

```js

let user = {
    name: "John",
    surname: "Smith",

    set fullName(value) {
        [this.name, this.surname] = value.split(" ");
    },

    get fullName() {
        return `${this.name} ${this.surname}`;
    }
};

let admin = {
    __proto__: user,
    isAdmin: true
};

console.log(admin.fullName); // John Smith (*)

// setter triggers!
admin.fullName = "Alice Cooper"; // (**)

console.log(admin.fullName); // Alice Cooper，admin 的内容被修改了
console.log(user.fullName);  // John Smith，user 的内容被保护了
```



## 3 原型中的this的值

在原型中，this的不受影响，在一个方法调用中，this的值始终都是点符号(.)的前面的对象。

```js
let animal = {
    walk() {
        if (!this.isSleeping) {
            console.log(`I walk`);
        }
    },
    sleep() {
        this.isSleeping = true;
    }
};

let rabbit = {
    name: "White Rabbit",
    __proto__: animal
};

// 修改 rabbit.isSleeping
// sleep() 的this 指的是 rabbit， 所以下面的animal.isSleeping 得到 undefined。
rabbit.sleep();

console.log(rabbit.isSleeping); // true
console.log(animal.isSleeping); // undefined（原型中没有此属性）
```

原型中的方法是共享的，但是状态不是。



## 4 for...in 循环

for...in循环也会迭代继承中的属性，例如：

```js
let animal = {
    eats: true
};

let rabbit = {
    jumps: true,
    __proto__: animal
};

// Object.keys 只返回自己的 key
console.log(Object.keys(rabbit)); // jumps

// for..in 会遍历自己以及继承的键
for (let prop in rabbit) console.log(prop); // jumps，然后是 eats
```

如果我们向排除掉继承中的key，JS有一个内建的方法：`obj.hasOwnProperty(key)`，如果obj有自己的key（非继承），则返回true。

可以使用这个内建方法过滤掉继承的属性：

```js

let animal = {
    eats: true
};

let rabbit = {
    jumps: true,
    __proto__: animal
};

for (let prop in rabbit) {
    let isOwn = rabbit.hasOwnProperty(prop);

    if (isOwn) {
        console.log(`Our: ${prop}`); // Our: jumps
    } else {
        console.log(`Inherited: ${prop}`); // Inherited: eats
    }
}
```



> 值得注意的是：上述代码中的`rabbit.hasOwnProperty` 方法也是继承自2`Object`的方法，但是当我们在使用for...in循环是为什么没有列出这个属性呢？
>
> 
>
> 这是因为`hasOwnProperty`这个属性是不可枚举的。`enumerable:false`。



几乎所有其他键/值获取方法，像是`Object.keys`或者是`Object.values` 都会忽略继承的属性。他们只会对对象自身操作。



## 5 总结

- 在JS中所有的对象多有一个隐藏的属性：`[[Prototype]]`属性，他要么是一个对象要么是`null`。

- `[[Prototype]]`引用的对象成为原型。

- 如果我们调用 obj.method()，而且 method 是从原型中获取的，this 仍然会引用 obj。因此，方法始终与当前对象一起使用，即使方法是继承的。
- `for..in` 循环在其自身和继承的属性上进行迭代。所有其他的键/值获取方法仅对对象本身起作用。
