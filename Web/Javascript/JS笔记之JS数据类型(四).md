---
title: JS笔记之JS数据类型(四)
author: chaizz
date: 2023-2-6 15:38:59
tags: JavaScript
photo: ["https://tc.chaizz.com/ec55444c4a1211edac740242ac190002.png"]
---

​                    

<!--more-->



# JS笔记之JS数据类型(四)

## 1 可迭代对象定义

可迭代对象是数组的泛化，任何对象都可以被定制为在 for...of循环使用的对象。

在JS中可迭代的内建对象有很多，例如数组，字符串等。



### 1.1 创建可迭代对象

例如我们要让下面的对象，能够使用for...of循环得到下面的结果。

```js
let range = {
    from: 1,
    to: 5
};

// 希望得到的结果
// for (let num of range) ... num = 1，2, 3,4，5
```



使用内建方法：Symbol.iterator （一个专门使对象可迭代的内建Symbol） 

1. 给range对象添加一个属性， 即这个方法Symbol.iterator ，这个方法返回一个迭代器：一个有next方法的对象。
2. for...of 适用于这个被返回的对象。
3. 当for...of循环，希望取得下一个值的时候，就会调用这个和next方法。
4. next方法返回的结构必须是{done:Boolean, value:any} 当done为true时，循环结束， 否则value是下一个值。

### 1.2 具体的实现方法：

```js
let range = {
  from: 1,
  to: 5
};

// 1. for..of 调用首先会调用这个：
range[Symbol.iterator] = function() {

  // ……它返回迭代器对象（iterator object）：
  // 2. 接下来，for..of 仅与下面的迭代器对象一起工作，要求它提供下一个值
  return {
    current: this.from,
    last: this.to,

    // 3. next() 在 for..of 的每一轮循环迭代中被调用
    next() {
      // 4. 它将会返回 {done:.., value :...} 格式的对象
      if (this.current <= this.last) {
        return { done: false, value: this.current++ };
      } else {
        return { done: true };
      }
    }
  };
};

// 现在它可以运行了！
for (let num of range) {
  alert(num); // 1, 然后是 2, 3, 4, 5
}
```

> range 本身没有next方法。而是通过symbol.iterator 创建了另一个对象，即迭代器对象，并且他的next会为迭代生成值。



**迭代器和要进行迭代的对象是分开的，技术上也可以进行合并。**

```js
let range = {
  from: 1,
  to: 5,

  [Symbol.iterator]() {
    this.current = this.from;
    return this;
  },

  next() {
    if (this.current <= this.to) {
      return { done: false, value: this.current++ };
    } else {
      return { done: true };
    }
  }
};

for (let num of range) {
  alert(num); // 1, 然后是 2, 3, 4, 5
}
```

现在 range的Symbol.iterator 属性，返回的是自身，即range有一个next方法属性。

### 1.3 字符串也是可迭代的

```js
for (let char of "test") {
  // 触发 4 次，每个字符一次
  alert( char ); // t, then e, then s, then t
}
```

### 1.4 显示的调用迭代器

```js
let str = "Hello";

// 和 for..of 做相同的事
// for (let char of str) alert(char);

let iterator = str[Symbol.iterator]();

while (true) {
  let result = iterator.next();
  if (result.done) break;
  alert(result.value); // 一个接一个地输出字符
}
```

在实际开发中很少这样做，但是这样做自由度更高，我们可以在任意迭代的过程中，暂停，并做一些其他的处理。



### 1.5 可迭代和类数组 （array-like）

可迭代：实现了上述 symbol.iterator 方法的对象。

array-like：是有索引和length属性的对象，所以他们看起来像数组。

可迭代对象通常都不是数组，他们没有push和pop方法，如果要将一个可迭代对象转化为数组，要怎么实现？



### 1.6 array.from

array.from 接收一个可迭代和类数组的值，并从中获取一个真正的数组，然后对齐调用数组的方法。

array.from 也接收一个可选的函数作为参数，该函数会在对象中的元素被添加到数组前，被应用于每个元素，此外 thisArg 允许我们为该函数设置 this。

```js
// 假设 range 来自上文例子中
// 求每个数的平方
let arr = Array.from(range, num => num * num);
alert(arr); // 1,4,9,16,25


// 将 str 拆分为字符数组
let str = '𝒳😂';
let chars = Array.from(str);
alert(chars[0]); // 𝒳
alert(chars[1]); // 😂
alert(chars.length); // 2
```

array.from 相对于str.split() 要精简许多。



## 2 总结：

可迭代对象必须实现symbol.iterator方法。他的结果是一个对象，被称为迭代器，由这个对象的next属性进一步处理迭代过程。

symbol.iterator 由for...of自动调用，但是我们也可以手动调用。

有索引和length属性的兑现我们称之为类数组对象，这种对象可能有其他的方法，但是没有数组的内建方法。

array.from(obj[,mapFn, thisAsg]) 将可迭代对象或者类数组对象转化为真正的数组, 然后我们就可以使用对应的数组方法。可选参数 mapFn 和 thisArg 允许我们将函数应用到每个元素。

