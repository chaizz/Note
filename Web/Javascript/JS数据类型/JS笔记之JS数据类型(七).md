---
title: JS笔记之JS数据类型(七)
author: chaizz
date: 2023-2-10 
tags: JavaScript
photo: ["https://tc.chaizz.com/ec55444c4a1211edac740242ac190002.png"]
---

​                    

<!--more-->

# JS笔记之JS数据类型(七)

## 1 日期和时间

### 1.1 创建日期

```js
// 语法
let now = new Date()
```

### 1.2 Date() 接收参数类型

- int类型：代表距离 1970 年 1 月 1 日 UTC+0 之后经过的毫秒数，可以为负数。

  ```js
  // 0 表示 01.01.1970 UTC+0
  let Jan01_1970 = new Date(0);
  alert( Jan01_1970 );
  
  // 现在增加 24 小时，得到 02.01.1970 UTC+0
  let Jan02_1970 = new Date(24 * 3600 * 1000);
  alert( Jan02_1970 );
  
  // 31 Dec 1969
  let Dec31_1969 = new Date(-24 * 3600 * 1000);
  alert( Dec31_1969 );
  ```

- str类型：将会自动解析为 时间字符串

  ```js
  let date = new Date("2017-01-26");
  alert(date);
  // 未指定具体时间，所以假定时间为格林尼治标准时间（GMT）的午夜零点
  // 并根据运行代码时的用户的时区进行调整
  // 因此，结果可能是
  // Thu Jan 26 2017 11:00:00 GMT+1100 (Australian Eastern Daylight Time)
  // 或
  // Wed Jan 25 2017 16:00:00 GMT-0800 (Pacific Standard Time)
  ```

- year, month, date, hours, minutes, seconds, ms：使用当前时区的给定组件创建日期，只有前两个参数是必须的，

  - year 应该是四位数，为了兼容也接受两位数，例如：98 应该等于1998，强烈建议使用四位数。
  - month 计数从 0（一月）开始，到 11（十二月）结束。
  - date 是当月的具体某一天，如果缺失，默认为1。
  - hours, minutes, seconds, ms 如果缺失默认为0.

  ```js
  new Date(2011, 0, 1, 0, 0, 0, 0); // 1 Jan 2011, 00:00:00
  new Date(2011, 0, 1); // 同样，时分秒等均为默认值 0
  // 时间度量精确到1毫秒
  let date = new Date(2011, 0, 1, 2, 3, 4, 567);
  alert( date ); // 1.01.2011, 02:03:04.567
  ```



### 1.3 获取日期组件

- getFullYear：从Date对象中获取年（四位数）。
- getMonth()：获取月份，从0 到11。
- getDate() 获取具体的日期。1 到 31。
- getHouers() 获取当天的小时。
- getMinutes() 获取分钟。
- getSeconds() 获取秒
- getMilliseconds() 获取毫秒。
- getDay() 获取一周中的第几天， 星期几，从0 到 6， 星期天是0。
- getTime() 返回自1970-1-1 00:00:00 UTC+0 开始到现在所经过的毫秒数。
- getTimezoneOffset() 返回UTC时间与本地时间的误差，以分钟为单位。



### 1.4 设置日期组件

- setFullYear(year, [month], [date])
- setMonth(month, [date])
- setDate(date)
- setHours(hour, [min], [sec], [ms])
- setMinutes(min, [sec], [ms])
- setSeconds(sec, [ms])
- setMilliseconds(ms)
- setTime(milliseconds)（使用自 1970-01-01 00:00:00 UTC+0 以来的毫秒数来设置整个日期）

以上方法除了 setTime() 方法，都有UTC变体，例如：setUTCHours()。

其中有的方法接受多个参数，不过不填的话，默认按照当前的时间。例如：

```js
let today = new Date();

today.setHours(0);
alert(today); // 日期依然是今天，但是小时数被改为了 0  2023-02-09T16:53:33.757Z

today.setHours(0, 0, 0, 0);
alert(today); // 日期依然是今天，时间为 00:00:00。 2023-02-09T16:00:00.000Z
```



### 1.5 自动校准

Date 的参数超过范围，它本身会自动校准。

假设我们要在日期 “28 Feb 2016” 上加 2 天。结果可能是 “2 Mar” 或 “1 Mar”，因为存在闰年。但是我们不需要考虑这些，只需要直接加 2 天，剩下的 Date 对象会帮我们处理：

```js
let date = new Date(2016, 1, 28);
date.setDate(date.getDate() + 2);

alert( date ); // 1 Mar 2016
```

这个特性经常获取给定时间段后的时间。



### 1.6 日期转化为数字，日期差值。

当Date被转化为数字时， 获取的是时间戳，与getTime()相同，

```js
let date = new Date();
// 使用一元运算符 将Date对象转化为数字 
alert(+date);
```



日期可以相减，结果是以毫秒为单位的时间差，可以用作测试代码的执行速度。例如：

```js
let start = new Date(); // 开始测量时间

// do the job
for (let i = 0; i < 100000; i++) {
  let doSomething = i * i * i;
}

let end = new Date(); // 结束测量时间

alert( `The loop took ${end - start} ms` );
```



### 1.7 Date.new()

在上文中，创建了两个Date对象，用来计算时间间隔，在JS中还有一个方法Date.new()，他会返回当前时间戳，相等于 new Date().getTime() 但是不会创建中间的Date对象，因此更快，不会对垃圾回收造成额外的压力，所以在使用JS编写游戏或者其他有性能要求的场景会使用到。

因此上文中的 计算代码执行速度这种方式最好：

```js
let start = Date.now(); // 从 1 Jan 1970 至今的时间戳

// do the job
for (let i = 0; i < 100000; i++) {
  let doSomething = i * i * i;
}

let end = Date.now(); // 完成

alert( `The loop took ${end - start} ms` ); // 相减的是时间戳，而不是日期
```



### 1.8 对字符串调用Date.parse

根据时间字符串获取对应的时间戳，字符串的格式为：YYYY-MM-DDTHH:mm:ss.sssZ，其中：T 是分隔符，Z 为 +- hh:mm 格式的时区，单个字符代表 UTC+0 时区，简短的形式也可以：YYYY，或者是 YYYY-MM-DD 或者YYYY-MM。

Date.parse 返回的结果是时间戳，从 1970-01-01 00:00:00 起所经过的毫秒数，如果给点的字符串不正确，则返回NaN。



















