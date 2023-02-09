---
title: JS笔记之JS数据类型(五)
author: chaizz
date: 2023-2-6 15:38:59
tags: JavaScript
photo: ["https://tc.chaizz.com/ec55444c4a1211edac740242ac190002.png"]
---

​                    

<!--more-->

# JS笔记之JS数据类型(五)

## 1 Map

map 是一个带键的数据项的集合，他和object很像， 但是map允许任意类型的键，而对象的键只能是 字符串和symbol，不管用什么当做对象的key（除了symbol），都会被js转化为字符串。

### 1.1 方法

- new Map() ：创建一个map。
- map.set(key, value)：根据键设置值。map在创建时保留了值插入的顺序。
- map.get(key)：根据键来返回值，如果不存在key，则返回undefined。
- map.has(key)：如果key存在返回true，不存在返回false。
- map.delete(key)：删除指定的key。
- map.clear()：清空map。
- map.size：返回当前元素的个数。

在使用map获取key的值或者设置值时 应该使用 map.get() 或者 map.set() 。

```js
let map = new Map();

map.set('1', 'str1');   // 字符串键
map.set(1, 'num1');     // 数字键
map.set(true, 'bool1'); // 布尔值键

// Map 则会保留键的类型，所以下面这两个结果不同：
alert( map.get(1)   ); // 'num1'
alert( map.get('1') ); // 'str1'

alert( map.size ); // 3

// map 使用对象作为key
let username = {'name':'john'};
let visitsCountMap = new Map();
visitsCountMap.set('john', 123)

```

map比较键的方法：使用sameValueZero算法来比较键是否相等，他和严格等于 === 差不多，但区别是 NaN是等于NaN的，所以NaN也可以被用作键。

map 在设置key的时候返回的是他自身，所以可以被链式调用，例如：

```js
let mapObj = new Map();
mapObj.set('name', ‘张三).set('age', 18).set('sex', 'm');
```

### 1.2 Map 迭代

在map里面循环可以使用下面的三个方法：

- map.keys() 遍历并返回一个包含所有键的可迭代对象。
- map.values() 遍历并返回一个包含所有值的可迭代对象。
- map.entries() 遍历并返回一个包含所有实体 [key, value] 的可迭代对象，for...of 在默认情况下才 就是使用的此方法。
- map.forEach()  和数组一样，对每个键值对执行一个函数。

  ```js
  // 对每个键值对 (key, value) 运行 forEach 函数
  recipeMap.forEach( (value, key, map) => {
    alert(`${key}: ${value}`); // cucumber: 500 etc
  });
  ```



### 1.3 将对象转化为Map Object.entries

当new 一个Map() 对象时，我们可以使用一个带键值对的数组（或者其他的可迭代对象）进行初始化，例如：

```js
// 键值对 [key, value] 数组
let map = new Map([
  ['1',  'str1'],
  [1,    'num1'],
  [true, 'bool1']
]);

alert( map.get('1') ); // str1
```

如果使用普通的对象来创建map，我们可以使用内建方法 Object.entries()obj，该方法返回对象的键值对数组。



### 1.4 将Map转化为建对象 Object.fromEntries(obj)

它与 Object.entries(obj)是相反的，给定一个具有[key, value] 的数组，他会根据数组创建一个对象。

```js
let prices = Object.fromEntries([
  ['banana', 1],
  ['orange', 2],
  ['meat', 4]
]);

// 现在 prices = { banana: 1, orange: 2, meat: 4 }
alert(prices.orange); // 2
```

例如 将一个map的数组转化为一个普通对象传输给第三方的接口

```js
let obj = Object.fromEntries(map)
```

## 2 set 

set 是一个特殊的类型集合 -- 值的集合，他没有键，而且他的值只能出现一次。

### 2.1 方法

- new Set(iterable)：  创建一个set，如果提供了一个可迭代对象（通常是数组），将会从数组里面复制到set中。有重复的值，自动删除。
- set.add(value)：添加一个值返回set 本身，可以像map那样链式调用。
- set.delete(value)： 删除值，存在返回true，不存在返回false。
- set.has(value)：如果value在set中，返回true，不在返回false。
- set.clear()：清空set。
- set.size：返回当前元素的个数。

### 2.2 set 迭代

可以使用for...of 或者 forEach 来迭代set

```js
let set = new Set(["oranges", "apples", "bananas"]);

for (let value of set) alert(value);

// 与 forEach 相同：
set.forEach((value, valueAgain, set) => {
  alert(value);
});
```

在forEach的回调函数中有三个参数， 一个是value，另一个也是 value，最后是目标对象。

**在map中的迭代方法在set中同样适用。**



## 3 WeakMap 

JS垃圾回收中JS引擎在值可达和被可能引用的时候，会将值保存在内存中。

```js
let john = { name: "John" };

// 该对象能被访问，john 是它的引用

// 覆盖引用
john = null;

// 该对象将会被从内存中清除
```

当某个对象作为map的键的时候， 我们也认为改对象是可达的，他会占用内存，且不会被垃圾回收。

```js
let john = { name: "John" };

let map = new Map();
map.set(john, "...");

john = null; // 覆盖引用

// john 被存储在了 map 中，
// 我们可以使用 map.keys() 来获取它，所以他不会被垃圾回收
```

## 4 WeakSet



## 5 总结

- map 是一个带有键的数据的集合。与普通的对象不同的是，他的键可以是任意数据类型。有不同结构的方法，例如：map.size

- set 是一组唯一值的集合。

- 在map和set 迭代总是按照插入的顺序，所以不能说他们的元素是无序的，但也不能对元素重新排序，也不能按照编号来取数据。
