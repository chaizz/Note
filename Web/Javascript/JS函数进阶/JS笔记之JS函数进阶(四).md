---
title: JS笔记之JS函数进阶(四)
author: chaizz
date: 2023-2-20
tags: JavaScript
photo: ["https://origin.chaizz.com/ec55444c4a1211edac740242ac190002.png"]
---

​         

<!--more-->

# JS笔记之JS函数进阶(四)



## 1 setTimeout 和 setInterval 

语法格式

```js
// setTimeout 第一个参数：一个函数或者是字符串代码(这个不常用)，第二个参数：时间间隔，代表多少秒后直行，剩下的参数：代表函数的参数。
let timer = setTimeout(() => {}, timeout [, arg1, arg2]);

// 和setTimeout 参数一致，只不过代表的含义为重复执行该函数，interval 是重复的间隔。
let timer2 = setInterval(() => {}, interval [, arg1, arg2]);
```

停止计时器使用clearTimeout(timer) 和 clearInterval(timer2)。



使用 clearInterval 函数的调用间隔实际会代码设置的调用间隔要短，因为是在函数开始时就开始计算时间，这个间隔时间包括函数执行的时间，如果函数执行的比较慢，name就会等待函数执行完成，如果函数的执行时间小于执行间隔，那么实际的执行间隔就是：函数的执行时间+剩余的时间= Interval。

使用clearTimeout 则不会出现这个问题，他是等待函数执行完毕，在进行下一次的函数调用。

> 当一个函数传入 setInterval/setTimeout 时，将为其创建一个内部引用，并保存在调度程序中。这样，即使这个函数没有其他引用，也能防止垃圾回收器（GC）将其回收。



## 2 嵌套setTimeout 

使用clearInterval 调用函数每次间隔都是一致的，如果想要更加的灵活的设置函数执行之间的间隔，可以将clearInterval 进行嵌套调用。一个常用的场景就是，我们调用接口获取失败后重试，可以设置不同的访问间隔，避免造成服务器压力过大。例如：

```js
let delay = 5000;
let timerId = setTimeout(function request() {
    console.log('发送请求！');
    if (false) {
        // 如果请求失败，下一次执行的间隔是当前的 2 倍
        delay *= 2;
    }
    timerId = setTimeout(request, delay);
}, delay);
```



## 3 零延时的setTimeout

setTimeout(func, 0) 或者是 setTimeout(func)，这种特性，叫做零延时调度，但是他需要在哦脚本执行完毕后执行。例如

```js
setTimeout(() => alert("World"));
alert("Hello");
```

以上的代码显示输出 Hello， 在输出 World。 

> 但是在浏览器环境中，零延时并不为零，嵌套定时器的运行频率是受限制的。根据 HTML5 标准 所讲：“经过 5 重嵌套定时器之后，时间间隔被强制设定为至少 4 毫秒”。



**任何 setTimeout 都只会在当前代码执行完毕之后才会执行。**



## 4 总结

- setTimeout(func, delay, ...args) 和 setInterval(func, delay, ...args) 方法允许我们在 delay 毫秒之后运行 func 一次或以 delay 毫秒为时间间隔周期性运行 func。要取消函数的执行，我们应该调用 clearInterval/clearTimeout，并将 setInterval/setTimeout 返回的值作为入参传入。
- 嵌套的 setTimeout 比 setInterval 用起来更加灵活，允许我们更精确地设置两次执行之间的时间。
  零延时调度 setTimeout(func, 0)（与 setTimeout(func) 相同）用来调度需要尽快执行的调用，但是会在当前脚本执行完成后进行调用。
- 浏览器会将 setTimeout 或 setInterval 的五层或更多层嵌套调用（调用五次之后）的最小延时限制在 4ms。这是历史遗留问题。
- 请注意，所有的调度方法都不能 保证 确切的延时
  - 例如，浏览器内的计时器可能由于许多原因而变慢：
  - CPU 过载。
  - 浏览器页签处于后台模式。
  - 笔记本电脑用的是省电模式。
  - 所有这些因素，可能会将定时器的最小计时器分辨率（最小延迟）增加到 300ms 甚至 1000ms，具体以浏览器及其设置为准。









