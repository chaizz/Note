---
title: JS笔记之JS数据类型(八)
author: chaizz
date: 2023-2-10 
tags: JavaScript
photo: ["https://tc.chaizz.com/ec55444c4a1211edac740242ac190002.png"]
---

​                    

<!--more-->



JSON.stringify(obj)

JSON编码的对象与JS对象的字面量有一些区别：

- 字符串使用双引号，没有单引号或者反引号。
- 对象的属性名称也是双引号的，是强制性的。

JSON支持以下的数据类型：

- Objects {...}
- Arrays [...]
- Strings ' '
- numbers 1
- boolean  true false
- null

JSON是语言无关的数据规范，所以一些属于JS的对象属性将会被忽略掉，例如：

- 函数
- Symbol
- undefined

```js
let user = {
    sayHi() { // 被忽略
        console.log("Hello");
    },
    [Symbol("id")]: 123, // 被忽略
    something: undefined // 被忽略
};

console.log( JSON.stringify(user) ); // {}（空对象）
```

> **Tips：在使用JSON.stringify(obj) 时（仅包含单个参数），对象中不能有循环引用。**



排除和转换 replacer

JSON.stringify 的完整语法是：

```js
let json = JSON.stringify(value[, replacer, space])

// value：要编码的值
// reolacer：要编码的属性数组，或者映射函数。 function(key, value)
// sapce：要格式化的空格数量
```

如果要过滤掉循环引用， 可以使用JSON.stringify的第二个参数：replacer，如果我们传递一个属性数组给他，name只有这些属性会被编码。

```js
let room = {
    number: 23
};

let meetup = {
    title: "Conference",
    participants: [{name: "John"}, {name: "Alice"}],
    place: room // meetup 引用了 room
};

room.occupiedBy = meetup; // room 引用了 meetup

console.log( JSON.stringify(meetup, ['title', 'participants']) );
// {"title":"Conference","participants":[{},{}]}
```

在上文的例字中，因为只设置了 participants和title， 所以输出内容中的 participants的下一级为空。

可以使用一个函数来保证转换的数据不包括循环引用的所有属性：

```js
let room = {
    number: 23
};

let meetup = {
    title: "Conference",
    participants: [{name: "John"}, {name: "Alice"}],
    place: room // meetup 引用了 room
};

room.occupiedBy = meetup; // room 引用了 meetup

console.log( JSON.stringify(meetup, function replacer(key, value) {
    console.log(`${key}: ${value}`);
    return (key == 'occupiedBy') ? undefined : value;
}));

// : [object Object]
// title: Conference                                                                       
// participants: [object Object],[object Object]                                           
// 0: [object Object]                                                                     
// name: John                                                                             
// 1: [object Object]                                                                    
// name: Alice                                                                             
// place: [object Object]                                                                 
// number: 23                                                                             
// occupiedBy: [object Object]                                                             
// {"title":"Conference","participants":[{"name":"John"},{"name":"Alice"}],"place":{"number":23}}
```



格式化 space

JSON.stringif 的第三个参数是优化格式的空格数量。也可以是字符串，字符串用于缩进而不是空格的数量。spaces 参数仅用于日志记录和美化输出。

```js
let room = {
    number: 23
};

let meetup = {
    title: "Conference",
    participants: [{name: "John"}, {name: "Alice"}],
    place: room // meetup 引用了 room
};

console.log(JSON.stringify(meetup, null, "===="))


// {
// ===="title": "Conference",
// ===="participants": [
// ========{
// ============"name": "John"
// ========},
// ========{
// ============"name": "Alice"
//     ========}
// ====],
// ===="place": {
// ========"number": 23
// ====}
// }
```



自定义 toJson

toString 可以进行字符串转化， 对象也可以自定义toJson来进行JSON转换，如果可用，JSON.stringify 则会自动调用它。 在Python 输出类的字符串形式， 使用魔术方法`__str__`和`__repr__`。

```js
let room = {
    number: 23,
    toJSON() {
        return this.number;
    }
};

let meetup = {
    title: "Conference",
    room
};
console.log( JSON.stringify(room) ); // 23
console.log( JSON.stringify(meetup) );
```

toJson 既可以用于直接调用JSON.stringify， 也可以在嵌套在另一个对象时，被调用。



JSON.parse

将JSON字符串要解码JS对象

```js
let value = JSON.parse(str, [reviver]);
// str：要解析的字符串
// reviver：可选的函数参数，函数将每个（key, value）对调用，并可以对值进行转换
```

```js
let userData = '{ "name": "John", "age": 35, "isAdmin": false, "friends": [0,1,2,3] }';
let user = JSON.parse(userData);
console.log( user.friends[1] ); // 1
```



当要解析的字符串包括日期字符串时，JSON.parse 就无法解析，这是我们可以通过第二个参数，定义一个函数来判断key。

```js
let schedule = `{
  "meetups": [
    {"title":"Conference","date":"2017-11-30T12:00:00.000Z"},
    {"title":"Birthday","date":"2017-04-18T12:00:00.000Z"}
  ]
}`;

schedule = JSON.parse(schedule, function(key, value) {
    if (key == 'date') return new Date(value);
    return value;
});

console.log( schedule.meetups[1].date.getDate() ); // 正常运行了！
```

函数在嵌套中同样适用。



总结

- JSON 支持 object，array，string，number，boolean 和 null。
- 如果独享具有toJson方法，那么会被 JSON.stringify 调用。