---
title: JS笔记之JS数据类型(三)
author: chaizz
date: 2023-1-31 17:10:52
tags: JavaScript
photo: ["https://origin.chaizz.com/ec55444c4a1211edac740242ac190002.png"]
---

​                    

<!--more-->

# JS笔记之JS数据类型(三)

## 1 数组

在JS中数组被定义为：[], 可以存放任意数据类型的元素。

### 1.1 at

获取数组最后一个元素，在Python语言中可以使用 list1[-1]，但是JS不可以这样使用，他提供了一个专门的方法：at， 这个方法在旧的浏览器上存在兼容问题。

如果 `i >= 0` 那么 `arr[i]` 和 `arr.at(i) `结果一致。 如果i为负数，`arr.at(i)` 则从后往前数。



### 1.2 pop/push, shift/unshift 方法

#### 1.2.1 作用于数组末端的方法：

pop：取出并返回数组的最后一个元素， 和上面的 array.at(-1) 结果一致， 但是，pop()会把最后一个元素删除，原数组被修改。空数组返回undefined。

push：在数组的末端添加元素，与 array[array.length] = "" 结果一致

#### 1.2.2 作用于数组首端的方法：

shift：取出数组的第一个元素并返回它。

unshift：在数组的首端添加元素。

unshift 和 push 都可以添加多个元素： `unshift('a','b')`、`push('a','b')`。



数组的访问方式：array[0], 实际上是来自于对象的语法，它扩展了对象，并提供了特殊的方法，来处理有序结合以及length属性，但是本质上讲数组仍然是一个对象。

例如数组的复制，是通过引用来实现的：

```js
let fruits = ["Banana"]

let arr = fruits; // 通过引用复制 (两个变量引用的是相同的数组)

alert( arr === fruits ); // true

arr.push("Pear"); // 通过引用修改数组

alert( fruits ); // Banana, Pear — 现在有 2 项了
```



但是数组的内部实现是将数组的元素一个一个的存储在连续的内存区域，且针对其优化，使其效率更高。

但是我们在使用数组想使用对象那样的话，数组的优化就不存在了。如下==错误==的使用方法：

- 添加一个非数字的属性：array.test = 3。
- 制造空洞，比如添加array[0], 然后在添加array[9999]。
- 以倒叙填充数组 比如：arr[1000], arr[999]。

所以在使用数组的时候，将数组作用于有序数组的特殊结构，JS引擎内部经过特殊优化过，如果需要任意值，可能普通对象更合适。



### 1.3 性能

pop和push 方法运行的比较快，而shift unshift 运行的比较慢。首先查看四个方法在执行期间都执行了什么？

shift

1. 第一步从首端取出第一个元素，所以移除了array[0]的元素，

2. 还需要将后面的所有的元素的索引进行重新编号。1 改成0 2 改成 1。
3. 更新数组的属性：length

unshift 也和shift 一样 在array[0] 新增一个元素，修改所有元素的索引值，修改length属性。元素越多，性能越慢。

pop:

1. 删除最后一个元素。

pop 和 push 一样，不需要移动元素，



### 1.4 循环

普通的计数循环可以使用 `for (let i=0; i<array.length; i++) {...}`，在数组中可以使用`for (let x of array) {...}`。使用 for...of 不能获取元素得索引 只能获取元素。

还有一种方法：`for(let key in array){...}` 虽有效果也是一样的，但是会存在问题：

1. 循环会遍历所有属性，不仅仅是数字属性。在浏览器和其它环境中有一种称为“类数组”的对象，它们 **看似是数组**。也就是说，它们有 `length` 和索引属性，但是也可能有其它的非数字的属性和方法，这通常是我们不需要的。`for..in` 循环会把它们都列出来。所以如果我们需要处理类数组对象，这些“额外”的属性就会存在问题。
2. `for..in` 循环适用于普通对象，并且做了对应的优化。但是不适用于数组，因此速度要慢 10-100 倍。当然即使是这样也依然非常快。只有在遇到瓶颈时可能会有问题。但是我们仍然应该了解这其中的不同。

**索引遍历数组不推荐使用for...in。**



### 1.5 数组的length

准确地说数组的length，并不是数组元素得个数，二是数组最大索引加一。例如：

```js
let fruits = [];
fruits[123] = "Apple";

console.log( fruits.length ); // 124 输出的结果就输索引值 + 1
```

length的又一特性为 length **是可写的**，但是修改了length以后的数组是不可逆的。

```js
let arr = [1, 2, 3, 4, 5];

arr.length = 2; // 截断到只剩 2 个元素
alert( arr ); // [1, 2]

arr.length = 5; // 又把 length 加回来
alert( arr[3] ); // undefined：被截断的那些数值并没有回来
```



### 1.6 多维数组

数组里面的项也可以是数组，我们称之为多维数组，可以用来存储矩阵。

```js
let matrix = [
  [1, 2, 3],
  [4, 5, 6],
  [7, 8, 9]
];
```



### 1.7 toString

数组的toString会返回以逗号隔开的元素列表。

```js
alert( [] + 1 ); // "1"
alert( [1] + 1 ); // "11"
alert( [1,2] + 1 ); // "1,21"
// 这里 [] 就变成了一个空字符串，[1] 变成了 "1"，[1,2] 变成了 "1,2"。
```



### 1.8 不要使用 == 比较数组

如果我们使用 `==` 来比较数组，除非我们比较的是两个引用同一数组的变量，否则它们永远不相等。



## 2 数组方法

前面介绍了 shift/unshift/pop/push等在数组开头结尾添加删除数组方法，还有一些其他的方法

### 2.1 splice [拼接]

splice 可以添加、删除、插入元素

使用 `del array[1]`的问题是：虽然数组的元素被删除掉了，但是根据array.length 获取数组的长度， 还是原来的长度，使用splice不出出现这个问题。

splice的语法：

```js
// start：从数组的索引 start 开始，删除 deleteCount 个元素，并在当前位置插入 elem1, ..., elemN 最后返回被删除的元素组成的数组。
array.splice(start [, deleteCount, elem1, ..., elemN])
```

```js
// 删除
let arr = ["I", "study", "JavaScript"];
arr.splice(1, 1); // 从索引 1 开始删除 1 个元素
alert( arr ); // ["I", "JavaScript"]

// 替换
let arr = ["I", "study", "JavaScript", "right", "now"];
// 删除数组的前三项，并使用其他内容代替它们
arr.splice(0, 3, "Let's", "dance");
alert( arr ) // 现在 ["Let's", "dance", "right", "now"]

// 插入
let arr = ["I", "study", "JavaScript"];
// 从索引 2 开始
// 删除 0 个元素
// 然后插入 "complex" 和 "language"
arr.splice(2, 0, "complex", "language");
alert( arr ); // "I", "study", "complex", "language", "JavaScript"

// 允许负向索引
let arr = [1, 2, 5];
// 从索引 -1（尾端前一位）
// 删除 0 个元素，
// 然后插入 3 和 4
arr.splice(-1, 0, 3, 4);
alert( arr ); // 1,2,3,4,5
```



### 2.2 slice [切片]

语法

```js
// 它返回一个新数组，从索引 start 开始到 end [取头不取尾]  start 和 end 都可以是负数， 和字符串的slice方法一致。
arr.slice([start], [end])

let arr = ["t", "e", "s", "t"];
alert( arr.slice(1, 3) ); // e,s（复制从位置 1 到位置 3 的元素）
alert( arr.slice(-2) ); // s,t（复制从位置 -2 到尾端的元素）

arr.slice()  // 创建一个arr副本，以进行不影响原始数组的进一步转换。
```



### 2.3 concat

语法

```js
// 接收任意数量的参数， 数组或者是值都可以，结果是包含arr和添加的值的数组，如果argN 是数组，那么会复制argN。
arr.concat(arg1, arg2, ...)
```

```js
let arr = [1, 2];

// 从 arr 和 [3,4] 创建一个新数组
alert( arr.concat([3, 4]) ); // 1,2,3,4
// 从 arr、[3,4] 和 [5,6] 创建一个新数组
alert( arr.concat([3, 4], [5, 6]) ); // 1,2,3,4,5,6
// 从 arr、[3,4]、5 和 6 创建一个新数组
alert( arr.concat([3, 4], 5, 6) ); // 1,2,3,4,5,6
```



如果某个对象，具有Symbol.isConcatSpreadable属性， 那么concat会把这个对象当做数组一样来处理。

```js
let arr = [1, 2];

let arrayLike = {
  0: "something",
  1: "else",
  [Symbol.isConcatSpreadable]: true,
  length: 2
};

alert( arr.concat(arrayLike) ); // 1,2,something,else
```



### 2.4 forEach 遍历

语法

```js
// 允许数组的每个元素，都运行一个函数, 有结果的话会被忽略。
arr.forEach(function(item, index, array) {
  // ... do something with item
});
```

```js
// 对每个元素调用 alert
["Bilbo", "Gandalf", "Nazgul"].forEach(alert);

// 介绍了它们在目标数组中的位置：
["Bilbo", "Gandalf", "Nazgul"].forEach((item, index, array) => {
  alert(`${item} is at index ${index} in ${array}`);
});
```





### 2.5 indexOf/lastIndexOf/includes

在数组中进行搜索

arr.indexOf(item, from)：从索引from开始搜索item, 找到返回索引，找不到返回-1。

arr.includes(item, from)：从索引from开始搜索item, 找到返回true，找不到返回false。

arr.lastIndexOf(item, from)与 indexOf 一样， 但是是从右到左查找。

一般只会传入item, 从头搜索。在搜索进行比较的时候使用的是 严格比较'==='。

**indexOf 和 includes 在查找 NaN 的时候 是有区别的，indexOf无法找到NaN，而includes 可以找到。**



在数组中查找具有特定条件的对象

### 2.6 find/findIndex/findLastIndex

```js
// item 是数组中的元素，index 是元素得索引， array 是数组本身
let ret = arr.find(function(item, index, array) {
  // 如果返回 true，则返回 item 并停止迭代
  // 对于假值（falsy）的情况，则返回 undefined
})
```

```js
// 例如，我们有一个存储用户的数组，每个用户都有 id 和 name 字段。让我们找到 id == 1 的那个用户：
let users = [
  {id: 1, name: "John"},
  {id: 2, name: "Pete"},
  {id: 3, name: "Mary"}
];

let user = users.find(item => item.id == 1);

alert(user.name); // John
```

findIndex 和find 有相同的的语法，只不过当找到时，返回的元素的索引。如果没找到返回-1。

findLastIndex 方法类似于 findIndex，但从右向左搜索，类似于 lastIndexOf。



### 2.7 filter 

find 是查找数组中的符合条件返回true的第一个元素，如果需要匹配的有很多，使用fileter。

语法与find相同，但是filter返回的是匹配元素组成的数组，没找到返回空数组。

```js
let users = [
  {id: 1, name: "John"},
  {id: 2, name: "Pete"},
  {id: 3, name: "Mary"}
];

// 返回前两个用户的数组
let someUsers = users.filter(item => item.id < 3);

alert(someUsers.length); // 2
```



### 2.8 map (有用且经常使用)

map 他对每个数组都调用函数，并返回结果数组。

语法

```js
let result = arr.map(function(item, index, array) {
  // 返回新值而不是当前元素
})
```

```js
let lengths = ["Bilbo", "Gandalf", "Nazgul"].map(item => item.length);
console.log(lengths); // 5,7,6
```



### 2.9 sort(fn) 

对数组进行原位排序， 在数组内部，而不是生成一个新的数组， 返回排序后的数组。

语法

```js
let arr = [ 1, 2, 15 ];
// 该方法重新排列 arr 的内容
arr.sort();
alert( arr );  // 1, 15, 2
```

**上面的排序结果是默认进行字符串进行排序的。**

比较函数只需要返回一个正数表示“大于”，一个负数表示“小于”。

```js
// 对于数字的比较
let arr = [ 1, 2, 15 ];
arr.sort( (a, b) => a - b );

// 对于字符串的比较
let countries = ['Österreich', 'Andorra', 'Vietnam'];
countries.sort( (a, b) => a.localeCompare(b) ) 
```



### 2.10 reverse

颠倒数组的元素， 返回颠倒后的数组

```js
let arr = [1, 2, 3, 4, 5];
arr.reverse();
alert( arr ); // 5,4,3,2,1
```



### 2.11 split和join

split：将字符串根据特定的字符串分割，并返回组成的数组，split方法有一个可选的第二个数字参数 —— 对数组长度的限制。如果提供了，那么额外的元素会被忽略。但实际上它很少使用。

```js
// 拆分字母
let str = "test";
alert( str.split('') ); // t,e,s,t
```

join：与split相反，根据特定的字符将数组的元素组成一个字符串。

```js
let arr = ['Bilbo', 'Gandalf', 'Nazgul'];
let str = arr.join(';'); // 使用分号 ; 将数组粘合成字符串
alert( str ); // Bilbo;Gandalf;Nazgul
```



### 2.12 reduce/reduceRight

当我们需要遍历一个数组时，我们可以使用 for/forof/forEach，需要遍历并返回每个元素的数据时，可以使用map， arr.reduce和arr.reduceRught更复杂，用于根据数组计算单个值。

语法

```js
// accumulator —— 是上一个函数调用的结果，第一次等于 initial（如果提供了 initial 的话）。
// item —— 当前的数组元素。
// index —— 当前索引。
// arr —— 数组本身。

let value = arr.reduce(function(accumulator, item, index, array) {
  // ...
}, [initial]);
```

应用函数时，上一个函数的调用结果，将作为参数传递给下一个函数。如果没有初始值，那么 reduce 会将数组的第一个元素作为初始值，并从第二个元素开始迭代。**建议始终指定初始值。**

```js
let arr = [1, 2, 3, 4, 5];
let result = arr.reduce((sum, current) => sum + current, 0);
alert(result); // 15

// 1、在第一次运行时，sum 的值为初始值 initial（reduce 的最后一个参数），等于 0，current 是第一个数组元素，等于 1。所以函数运行的结果是 1。
// 2、在第二次运行时，sum = 1，我们将第二个数组元素（2）与其相加并返回。
// 3、在第三次运行中，sum = 3，我们继续把下一个元素与其相加，以此类推……
```

reduceRight 和 reduce 一样，只是总右到左。



### 2.13 Array.isArray

数组是基于对象的，所以不能使用 typeOf 准确地判断类型。因此有一种特殊的方法，用来判断数组是一个数组：Array.isArray(value)。如果value是数组则返回true，否则返回false。



### 2.14 thisArg

几乎所有调用函数的数组方法 —— 比如 `find`，`filter`，`map`，除了 `sort` 是一个特例，都接受一个可选的附加参数 `thisArg`。

```js
arr.find(func, thisArg);
arr.filter(func, thisArg);
arr.map(func, thisArg);
// ...
// thisArg 是可选的最后一个参数
```



## 3 总结

### 3.1 添加删除元素

push(...items)  向尾端添加 items 

pop()   从尾端删除元素

shift()   在首部删除元素

unshift(...items)  在首部添加 items

splice(pos, deleteCount, ...items) 从pos删除deleteCount并插入items

slice(start, end)  返回一个新数组，返回从start开始到end结束的数组，但不包括end。

concat(...items) 返回一个新数组，参数如果是数组，则将数组拷贝过来，如果不是数组，直接添加。

### 3.2 搜索元素

indexOf/lastIndoxOf(items, pos)  从pos索引位置开始判断数组中是否含有items, 有返回items索引，没有返回-1。

includes(value) 如果数组有value，则返回true，否则返回 false。

find/fliter(func) 通过方法过滤元素。返回使 func 返回 true 的第一个值/所有值。。

findIndex 返回索引值。

### 3.3 遍历元素

forEach(func)  对每个元素都调用func。

### 3.4 转换数组

map(func)  根据每个元素调用func的返回的结果创建一个新数组。

sort(func) 对数组进行原位排序， 然后返回他。

reverse() 原位（in-place）反转数组，然后返回它。

split/join  将字符串转换为数组并返回/ 将数组转化为字符串并返回。

reduce/reduceRight(func, initial)   对每个元素执行方法，并将前一个元素作为函数的参数。

### 3.5 其他

Array.isArray(value) 判断value是否为数组， 是返回true不是返回false

sort、reverse、splice 都是对数组本身操作，不会产生新的数组。

some(func)/every(func) 对数组的每个元素调用func,如果为任何/所有结果true，返回true，否则返回false。

```js
// 比较数组是否相等
function arraysEqual(arr1, arr2) {
  return arr1.length === arr2.length && arr1.every((value, index) => value === arr2[index]);
}

alert( arraysEqual([1, 2], [1, 2])); // true
```

fill(value， start, end)  从索引start到end用重复的value 填充数组。

copyWithin(target, start, end) 将从位置 start 到 end 的所有元素复制到 自身 的 target 位置（覆盖现有元素）。

arr.flat(depth)/arr.flatMap(fn) 从多维数组创建一个新的扁平数组。

of(element0[,element1[,elementN]]) 基于可变数量的参数创建一个新的 Array 实例，而不需要考虑参数的数量或类型。



























