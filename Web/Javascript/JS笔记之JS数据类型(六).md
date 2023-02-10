---
title: JS笔记之JS数据类型(六)
author: chaizz
date: 2023-2-6 15:38:59
tags: JavaScript
photo: ["https://tc.chaizz.com/ec55444c4a1211edac740242ac190002.png"]
---

​                    

<!--more-->

# JS笔记之JS数据类型(六)



解构赋值是一种特殊的语法，他使可迭代对象拆包值一系列变量中，而只需要其中的一部分（与python的拆包类似）。

## 1 在数组中的解构

### 1.1 基本语法

```js
let [var1, var2] = arr;
```

```js
// 我们有一个存放了名字和姓氏的数组
let arr = ["John", "Smith"]

// 解构赋值
// 设置 firstName = arr[0]
// 以及 surname = arr[1]
let [firstName, surname] = arr;

alert(firstName); // John
alert(surname);  // Smith
```

当结构语法配合spilt更加优雅

```js
let [firstName, surname] = "John Smith".split(' ');
```

**结构并不意味着破坏，原有的数据对象自身没有被修改。**

### 1.2 忽略使用逗号的元素

```js
// 不需要第二个元素， 剩下的元素也被跳过了。
let [firstName, , title] = ["Julius", "Caesar", "Consul", "of the Roman Republic"];
```



### 1.3 解构的元素可以赋值给任何对象

```js
let user = {};
[user.name, user.surname] = "John Smith".split(' ');

alert(user.name); // John
alert(user.surname); // Smith
```



> Tips：使用解构交换两个变量的值
>
> ```js
> let guest = "Jane";
> let admin = "Pete";
> 
> // 让我们来交换变量的值：使得 guest = Pete，admin = Jane
> [guest, admin] = [admin, guest];
> 
> alert(`${guest} ${admin}`); // Pete Jane（成功交换！）
> ```

### 1.4 使用 ... 解构

在解构时遇到很多元素，当左边没有右边长时，部分元素作为一个整体进行结构。

```js
let [name1, name2, ...rest] = ["Julius", "Caesar", "Consul", "of the Roman Republic"];

// rest 是包含从第三项开始的其余数组项的数组
alert(rest[0]); // Consul
alert(rest[1]); // of the Roman Republic
alert(rest.length); // 2
```

在Python中 是使用`*`代替`...`

```python
name1, name2, *rest = ["Julius", "Caesar", "Consul", "of the Roman Republic"];

// rest 是包含从第三项开始的其余数组项的数组
print(rest[0]); // Consul
print(rest[1]); // of the Roman Republic
print(len(rest)); // 2
```

当左边比右边长时， 多出的变量名默认为：undefined，也可以手动指定变量的默认值， 默认值可以是更复杂的表达式，或者是一个函数。这些表达式或者函数只有在变量未被赋值的时候在会执行计算。

```js
let [firstName, surname] = [];
alert(firstName); // undefined
alert(surname); // undefined

// 手动指定默认值
let [name = "Guest", surname = "Anonymous"] = ["Julius"];
alert(name);    // Julius（来自数组的值）
alert(surname); // Anonymous（默认值被使用了
```



## 2 在对象中的解构

### 2.1 基本语法

```js
let {var1, var2} = {var1:…, var2:…}
// 在等号右侧是一个已经存在的对象。
// 等号左侧包含了对象相应属性的一个类对象“模式（pattern）”。在最简单的情况下，等号左侧的就是 {...} 中的变量名列表。
```

```js
let options = {
  title: "Menu",
  width: 100,
  height: 200
};

let {title, width, height} = options;

alert(title);  // Menu
alert(width);  // 100
alert(height); // 200

// 对象的属性值 options.title options.width  options.theightitle  被赋值给 title width height
```



### 2.2 在等号左侧的 pattern 可以更加的复杂：指定属性和变量间的关系。

```js
// 如果我们想把一个属性赋值给另一个名字的变量，比如把 options.width 属性赋值给名为 w 的变量，那么我们可以使用冒号来设置变量名称：
let options = {
  title: "Menu",
  width: 100,
  height: 200
};

// { sourceProperty: targetVariable }
let {widthk: w = '222', height: h, title} = options;

// width -> w
// height -> h
// title -> title

alert(title);  // Menu
alert(w);      // 100
alert(h);      // 200
```

其中冒号的语法是：`对象中的某个属性名：具体的变量名`，如果`对象中的某个属性名` 不在要解构的对象中，赋值得到的结果是：undefined。 同样也可以设置默认值，默认值是任意表达式或者函数。

### 2.3 对象结构对于多余的属性同样可以使用：...

```js
let options = {
  title: "Menu",
  height: 200,
  width: 100
};

// title = 名为 title 的属性
// rest = 存有剩余属性的对象
let {title, ...rest} = options;

// 现在 title="Menu", rest={height: 200, width: 100}
alert(rest.height);  // 200
alert(rest.width);   // 100
```



在解构中如果预先定义了变量， 可能会遇到一些错误。

```js
let title, width, height;

// 这一行发生了错误
{title, width, height} = {title: "Menu", width: 200, height: 100};
```

主要的问题是 JS 把主代码流，中的 {...} 当做一个代码块，为了告诉 JavaScript 这不是一个代码块，我们可以把整个赋值表达式用括号 `(...)` 包起来：

```js
let title, width, height;

// 现在就可以了
({title, width, height} = {title: "Menu", width: 200, height: 100});

alert( title ); // Menu
```



## 3 嵌套解构

如果一个独享或者数组中包括其他的对象或者数组，可以使用更负责的嵌套解构进行赋值。

```js
let options = {
  size: {
    width: 100,
    height: 200
  },
  items: ["Cake", "Donut"],
  extra: true
};

// 为了清晰起见，解构赋值语句被写成多行的形式
let {
  size: { // 把 size 赋值到这里
    width,
    height
  },
  items: [item1, item2], // 把 items 赋值到这里
  title = "Menu" // 在对象中不存在（使用默认值）
} = options;

alert(title);  // Menu
alert(width);  // 100
alert(height); // 200
alert(item1);  // Cake
alert(item2);  // Donu
```



## 4 智能参数函数

有时候一个函数有很多的参数，大部分是可选的，我们可以使用一个对象来传递所有的参数，而函数负责把这个对象进行解构为对应的参数。

```js
let options = {
  title: "My menu",
  items: ["Item1", "Item2"]
};

function showMenu({
  title = "Untitled",
  width: w = 100,  // width goes to w
  height: h = 200, // height goes to h
  items: [item1, item2] // items first element goes to item1, second to item2
}) {
  alert( `${title} ${w} ${h}` ); // My Menu 100 200
  alert( item1 ); // Item1
  alert( item2 ); // Item2
}

showMenu(options);
```

上面输入的参数对象是确定了存在参数，如果想让所有的参数都是用默认值，那么在函数中直接传递一个空对象即可。

```js
showMenu({});
// 或者
function showMenu({ title = "Menu", width = 100, height = 200 } = {}) {
  alert( `${title} ${width} ${height}` );
}

showMenu(); // Menu 100 200
```



## 5 总结

解构可以将一个对象或者数组（只要是可迭代对象）拆开赋值到多个变量上。

解构对象的完成语法：

```js
let {prop : varName = default, ...rest} = object
// prop：代表对象的key
// varName：要复制的变量名
// default：变量的默认值
// ...rest：其余的属性组成的对象
```

解构数组的完整语法：

```js
let [item1 = default, item2, ...rest] = array

// item1：数组的第一个元素
// default：默认值
// item2：数组的第二个元素
// ...rest：其余的元素组成的数组
```

