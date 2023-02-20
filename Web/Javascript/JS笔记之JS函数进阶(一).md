---
title: JS笔记之JS函数进阶(一)
author: chaizz
date: 2023-2-15
tags: JavaScript
photo: ["https://tc.chaizz.com/ec55444c4a1211edac740242ac190002.png"]
---

<!--more-->

# JS笔记之JS函数进阶(一)

## 1 函数rest参数

在JS中很多内建的参数都支持传入无限个数的参数。

当我们自己定义一个函数时， 也可以转入无限个数的参数，但是只能根据形参的个数来获取真正的参数，如何实现接收无限个参数或者接收数组形式的参数？

可以在函数定义时声明一个数组来收集参数，语法格式为：`...变量名`， 这会声明一个数组，并指定一个名称为变量名，其中存有剩余的参数。三个点的含义就是将剩余的参数存储到指定的数组中。例如：

```js
function sumAll(...args) { // 数组名为 args
    let sum = 0;

    for (let arg of args) sum += arg;

    return sum;
}
```

`...args` 称为Rest 参数，他可以和其他的正常的函数同时使用，因为`...args` 会收集所有剩余的参数，因此，这种语法格式只能放在函数的末尾。

在Python函数支持位置参数和关键字参数，同样可以使用`*args` 来接收不限个数的位置参数或者是使用`**kwargs`来接收不限制个数的关键字参数，例如：

```python
def sum_all(*args: int):
    sum = 0
    for x in args:
        sum += x
    return sum


if __name__ == '__main__':
    print(sum_all(1, 2, 3, 4))  # 10
```

在JS中还有一个特殊的参数对象areguments，可以在函数中被访问，该对象以参数在参数列中的索引作为键，存储所有的参数。areguments是一个类数组，也是一个可迭代对象，他不支持数组方法，而且他始终包含所有的参数，无法向数组那样直接对参数截取。因此当接受很多参数时最好使用Rest参数。

> 另外箭头函数是没有areguments的。如果在剪头函数中访问 areguments 得到的是箭头函数的外部的普通函数的areguments。
>
> 即箭头函数没有本身的this以及 areguments。



## 2 函数Spread语法

在上文中Rest参数是从多个参数中得到一个类数组对象，而Spread则与之相反，当我们有一个数组，如何将它作为多个参数的函数中去？

Spread的语法和Rest参数语法很像，也是使用`...`，但是用途确实相反，当在函数中调用`...arr`时，他会把可迭代对象展开到参数列表中去，例如：

```js
let arr = [3, 5, 1];
console.log( Math.max(...arr) ); // 5（spread 语法把数组转换为参数列表）

// 同样可以传入多个可迭代对象
let arr1 = [1, -2, 3, 4];
let arr2 = [8, 3, -8, 1];
console.log( Math.max(...arr1, ...arr2) ); // 8

// 与常规的参数使用
let arr1 = [1, -2, 3, 4];
let arr2 = [8, 3, -8, 1];
console.log( Math.max(1, ...arr1, 2, ...arr2, 25) ); // 25
```

Spread 可以迭代任意的可迭代对象，例如字符串，Spread内部使用迭代器来收集元素，和for...of 的方式相同，例如：

```js
let str1 = "Hello";
console.log( [...str1] ); // H,e,l,l,o

// Array.from 将可迭代对象转换为数组
let str2 = "Hello";
console.log( Array.from(str2) ); // H,e,l,l,o
```

上述的例字中Array.from 和[...str] 得到的结果一致，但是他们之间还有一个细微的差别：

- Array.from(str) 适用于类数组对象也适用于可迭代对象。
- Spread 语法只适用于可迭代对象。

使用Spread也可以进行浅拷贝

```js
let arr = [1, 2, 3];

let arrCopy = [...arr]; // 将数组 spread 到参数列表中，然后将结果放到一个新数组

console.log(JSON.stringify(arr) === JSON.stringify(arrCopy)); // 结果是一致的
console.log(arr === arrCopy);  // 引用是不一致的

arr.push(4)
console.log(arr);
console.log(arrCopy);
```

使用同样的方式也可以复制一个对象

```js
let obj = { a: 1, b: 2, c: 3 };

let objCopy = { ...obj }; // 将对象 spread 到参数列表中
                          // 然后将结果返回到一个新对象

// 两个对象中的内容相同吗？
alert(JSON.stringify(obj) === JSON.stringify(objCopy)); // true

// 两个对象相等吗？
alert(obj === objCopy); // false (not same reference)

// 修改我们初始的对象不会修改副本：
obj.d = 4;
alert(JSON.stringify(obj)); // {"a":1,"b":2,"c":3,"d":4}
alert(JSON.stringify(objCopy)); // {"a":1,"b":2,"c":3}
```

这种方式比使用 let arrCopy = Object.assign([], arr) 复制数组，或使用 let objCopy = Object.assign({}, obj) 复制对象来说更为简便。因此，只要情况允许，我们倾向于使用它

## 3 总结

Rest 参数语法和Spread 语法的一个区别就是若`...`出现在函数参数列表的最后，那么他就是Rest参数，如果出现在函数调用或者类似的表达式中，那么就是Spread语法。

可以使用这两种语法轻松的转换参数列表和参数数组。
