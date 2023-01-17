---
title: JS笔记之JS对象基础知识(一)
author: chaizz
date: 2023-1-17 10:30:02
tags: JavaScript
photo: ["https://tc.chaizz.com/ec55444c4a1211edac740242ac190002.png"]
---

​          

<!--more-->

js 对象是用来存储键值对或者更复杂的实体信息，

1、创建空对象的两种方法

```js
let user = new Object(); // “构造函数” 的语法
let user = {};  // “字面量” 的语法
```

1.1 创建对象

```js
let user = {     // 一个对象
  name: 'John',  // 键 "name"，值 "John"
  age: 30,        // 键 "age"，值 30
};
```

对象属性的操作

```js
console.log(user.age); // 获取对象
user.age = 100  // 修改属性值
delete user.age // 使用delete 删除对象的属性
user.sex = 'man' // 新增属性值
```

创建对象也可以使用对个词语作为属性名

```js
let user = {
  name: 'John',
  age: 30,
  'likes birds': true,  // 多词属性名必须加引号
};
```

对象的最后一个属性应以逗号结尾，这叫做尾随（trailing）或悬挂（hanging）逗号。这样便于我们添加、删除和移动属性，因为所有的行都是相似的。

如果在对象中使用多词语属性，那么就不能使用【对象.属性】来获取对象的属性值， 必须使用方括号。

因为点符号要求是有效的变量标识符，所以不能包含空格、不以数字开头、也不包含特殊符号（除了`_`和`$`）

```js
console.log(user["likes birds"]);
```



1.2 计算属性

可以通获取变量来动态的创建对象属性，这叫做计算属性。

```js
let fruit = prompt("Which fruit to buy?", "apple");

let bag = {
  [fruit]: 5, // 属性名是从 fruit 变量中得到的
};

alert( bag.apple ); // 5 如果 fruit="apple"
```



1.3 属性值简写

```js
function makeUser(name, age) {
  return {
    name: name,
    age: age,
    // ……其他的属性
  };
}

let user = makeUser("John", 30);
alert(user.name); // John
```

在上述的例子中属性名和属性值一样，可以使用简写的形式，类似下面这种， 也可以进行混用。

```js
function makeUser(name, age) {
  return {
    name, // 与 name: name 相同
    age,  // 与 age: age 相同
    sex:'man', // 简写与不简写混用
    // ...
  };
}
let user = makeUser("John", 30);
alert(user.name); // John
```

1.4 属性名称限制

在定义变量的时候， 我们不能使用js的一些和关键字保留字，但是在定义对象的属性时， 是可以使用的。所以对象的属性名是没有限制的，属性名可以是任意的字符串或者是symbol。例如

```js
let obj = {
  0: "test" // 等同于 "0": "test"
};

// 都会输出相同的属性（数字 0 被转为字符串 "0"）
alert( obj["0"] ); // test
alert( obj[0] ); // test (相同的属性)
```



1.5 属性存在性测试， 'in' 操作符

在Python中获取一个对象的不存在的属性时会直接抛出异常，而js则不会，

``` python
dict_ex = {
    "a":'a',
    'b':'b'
}


class Ex:

    def a(self):
        return 'a'

    def b(self):
        return 'b'
    
    
if __name__ == '__main__':
    print(dict_ex['c'])   # 会直接提示  KeyError: 'c'
    Ex().c    # 会直接提示  AttributeError: 'Ex' object has no attribute 'c'
```

```js
let user = {};

alert( user.noSuchProperty === undefined ); // true 意思是没有这个属性
```

检查属性是否存在的操作符 `"in"`，语法格式：`'key' in object` 例如：

```js
let user = { name: "John", age: 30 };

alert( "age" in user ); // true，user.age 存在
alert( "blabla" in user ); // false，user.blabla 不存在。
```

使用`in` 和使用`===undefined` 判断属性是否存在的区别，那就是当一个对象的属性存在，但是他的值恰好就是`undefined`， 此时使用 `===undefined` 就无法准确地判断对象属性是否存在（这种情况也少之又少）。



1.6 for...in 循环

主要用来遍历对象的所有的键。

```js
for (key in object) {
  // 对此对象属性中的每个键执行的代码
}
```



1.7 对象有顺序吗？

有但不多，对像中的**整数的属性**会被排序，以升序排列，其他的属性会按照创建的顺序显示。例如：

```js
let codes = {
  "49": "Germany",
  "41": "Switzerland",
  "44": "Great Britain",
  // ..,
  "1": "USA"
};

for(let code in codes) {
  console.log(code); // 1, 41, 44, 49
}
```

整数属性的解释：这里的“整数属性”指的是一个可以在**不做任何更改**的情况下与一个整数进行相互转换的字符串，像是 "+49" 或者是 "1.3342" 就不行了。

所以为了解决以上的例子，能够按属性的定义顺序输出可以在属性名前加一个`+`号。

```js
let codes = {
  "+49": "Germany",
  "+41": "Switzerland",
  "+44": "Great Britain",
  // ..,
  "+1": "USA"
};

for (let code in codes) {
  console.log( +code ); // 49, 41, 44, 1
}
```

































































