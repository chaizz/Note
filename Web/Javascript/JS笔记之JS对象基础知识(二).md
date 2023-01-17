---
title: JS笔记之JS对象基础知识(二)
author: chaizz
date: 2023-1-17 13:38:55
tags: JavaScript
photo: ["https://tc.chaizz.com/ec55444c4a1211edac740242ac190002.png"]
---

​          

<!--more-->

JS笔记之JS对象基础知识(二)

1 对象的引用和复制

对象和原始类型（数字字符串布尔类型等）的区别就是对象是**通过引用** 存储和复制的，而原始类型总是作为一个整体复制。



1.1 JS赋值时会发生什么？

首先原始类型进行赋值时，message 和 phrase 是两个独立的变量。之间没有什么直接的关系。

```js
let message = "Hello!";
let phrase = message;
```

而对象不是这样的，一个对象被赋值给某个变量，这个变量存储的并不是对象的本身，而是这个对象的内存的地址。

就像一把钥匙对应一个柜子，这把钥匙可以对该柜子打开并添加、取走里面的内容，此时又根据这把钥匙重新配了一个一个钥匙，而新配的这把钥匙也能够打开这个柜子。 这两把钥匙都可以对这个柜子修改。

```js
let user = { name: 'John' };

let admin = user;

admin.name = 'Pete'; // 通过 "admin" 引用来修改

console.log(user.name); // 'Pete'，修改能通过 "user" 引用看到	
```

**当一个对象变量被复制 —— 引用被复制，而该对象自身并没有被复制。**

1.2 通过引用来比较

仅当两个对象为同一个对象时，两者才相等。

```js
let a = {};
let b = a; // 复制引用

console.log( a == b ); // true，都引用同一对象
console.log( a === b ); // true	
```

而这里我们创建了两个空对象，看起来都是空的对象，但他们并不相等。

```js
let a = {};
let b = {}; // 两个独立的对象

console.log( a == b ); // false
```



1.3 克隆与合并

如果想要复制一个全新对象，可以循环遍历原有的对象，然后建立新的对象并进行创建。

还可以使用 `Object.assign(dest, [src1, src2, src3...])` 来创建。

```js
let user = { name: "John" };

let permissions1 = { canView: true };
let permissions2 = { canEdit: true };

// 将 permissions1 和 permissions2 中的所有属性都拷贝到 user 中
Object.assign(user, permissions1, permissions2);

// 现在 user = { name: "John", canView: true, canEdit: true }
```

如果被拷贝的属性已经存在，怎会覆盖掉原有的属性。

以上的情况都是建立在对象的属性都是原始类型的情况下，但是有些对象可能包含其他的对象，如果使用`Object.assign()` 不会对深层的对象进行拷贝，而是引用。

```js
let user = {
  name: "John",
  sizes: {
    height: 182,
    width: 50
  }
};

let clone = Object.assign({}, user);

alert( user.sizes === clone.sizes ); // true，同一个对象

// user 和 clone 分享同一个 sizes
user.sizes.width++;       // 通过其中一个改变属性值
alert(clone.sizes.width); // 51，能从另外一个获取到变更后的结果
```

为了解决这个问题，并让 `user` 和 `clone` 成为两个真正独立的对象，我们应该使用一个拷贝循环来检查 `user[key]` 的每个值，如果它是一个对象，那么也复制它的结构。这就是所谓的“深拷贝”。可以使用递归实现， 或者也可以使用现成的轮子， lodash 库的 [_.cloneDeep(obj)](https://lodash.com/docs/4.17.15#cloneDeep)。



总结

对象通过引用被赋值和拷贝。换句话说，一个变量存储的不是“对象的值”，而是一个对值的“引用”（内存地址）。因此，拷贝此类变量或将其作为函数参数传递时，所拷贝的是引用，而不是对象本身。

所有通过被拷贝的引用的操作（如添加、删除属性）都作用在同一个对象上。

为了创建“真正的拷贝”（一个克隆），我们可以使用 `Object.assign` 来做所谓的“浅拷贝”（嵌套对象被通过引用进行拷贝）或者使用“深拷贝”函数，例如 [_.cloneDeep(obj)](https://lodash.com/docs#cloneDeep)。































