---
title: JS笔记之JS错误处理(一).md
author: chaizz
date: 2023-3-20
tags: JavaScript
photo: ["https://tc.chaizz.com/ec55444c4a1211edac740242ac190002.png"]
---

​         

<!--more-->

# JS笔记之JS错误处理(一)

在编程中很难能够一次性写出完美的代码，错误确实经常性的，在JS中可以使用try...catch能够使我们捕获错误。

## 1 try...catch语法

try...catch 结构由两部分组成：try 和 catch：

```js
try {

    // 代码...

} catch (err) {

    // 错误捕获

}
```

他的执行步骤是：

1. 首先执行try...的代码。
2. 如果第一步没有错误，则忽略catch(err)...的代码，跳过catch...的代码，执行try的末尾继续执行。
3. 如果第一步错误，则try...停止执行，代码则跳转带catch(err)的开头，变量err(可以是任何名称)将包含一个error对象，该对象包含了所发生事件的详细信息。

所以 try...catch(err)... try后面的代码不会导致程序停止。一些例子：

```js
// 没有 error 的例子：显示 alert (1) 和 (2)：
try {
    console.log('开始执行 try 中的内容');  // (1) <--
    // ...这里没有 error
    console.log('try 中的内容执行完毕');   // (2) <--
} catch (err) {
    console.log('catch 被忽略，因为没有 error'); // (3)
}

// 包含 error 的例子：显示 (1) 和 (3) 行的 alert 中的内容：
try {
    console.log('开始执行 try 中的内容');  // (1) <--
    lalala; // error，变量未定义！
    console.log('try 的末尾（未执行到此处）');  // (2)
} catch (err) {
    console.log(`出现了 error！`); // (3) <--
}
```



> Tips：try...catch... 仅仅对运行时的error有效，也就是说要想try...catch能够工作，代码必须是可执行的，如果包含语法错误，那么try..catch将无法工作。
>
> JS引擎首先会读取代码，然后运行它，在读取阶段发生的错误，被称为解析时间（parse-time）错误，并且无法修复，因为引擎无法理解代码。

所以try...catch只能处理有效代码中出现的错误，这列错误成为运行时的错误(runtime errors)，也被称为异常。

如果在"计划的"代码中发生异常，例如在setTimeout中，则try...catch不会捕获到异常。

```js
try {
    setTimeout(function () {
        noSuchVariable; // 脚本将在这里停止运行
    }, 1000);
} catch (err) {
    console.log("不工作");
}
```

因为 try...catch 包裹了计划要执行的函数，该函数本身要稍后才执行，这时引擎已经离开了try...catch 结构。

如果要捕获到计划的函数的异常，那么try...catch 必须在这个函数内。

```js
setTimeout(function () {
    try {
        noSuchVariable; // try...catch 处理 error 了！
    } catch {
        console.log("error 被在这里捕获了！");
    }
}, 1000);
```



## 2 Error对象

发生错误时JS会生成一个包含此error的详细信息的对象，然后将该对象作为参数传递给catch，对于所有的内建的 error对象有两个主要的属性: name、message。 

name:error的名称，例如对于一个未定义的变量，名称是“ReferenceError”。

message: 关于error的详细文字描述。

还有其他的非标准的属性，在大多数环境下可用，其中被广泛使用和支持的是：

stack: 代表当前的调用栈，用于调试目的的一个字符串，其中包含有关导致 error 的嵌套调用序列的信息。

```js
try {
    lalala; // error, variable is not defined!
} catch (err) {
    console.log(err.name); // ReferenceError
    console.log(err.message); // lalala is not defined
    console.log(err.stack); // ReferenceError: lalala is not defined at (...call stack)

    // 也可以将一个 error 作为整体显示出来
    // error 信息被转换为像 "name: message" 这样的字符串
    console.log(err); // ReferenceError: lalala is not defined
}
```

可选的catch绑定

这是一个新的特性，如果我们不需要error的信息，catch也可以忽略他。

```js
try {
    // ...
} catch { // <-- 没有 (err)
    // ...
}
```



## 3 try...catch的真实用例

当我们接受来自网络、服务器或是其他来源的json数据，我们会使用 JSON.parse(str)来讲字符串解析为JS对象。

但是如果json字符串无法解析，那么就会生成一个error，所以这个时候就需要try...catch来尝试解析这个json字符串，即使发生了错误我们也能主动的捕获。

```js
let json = "{ bad json }";

try {
    let user = JSON.parse(json); // <-- 当出现 error 时...
    console.log(user.name); // 不工作
} catch (err) {
    // ...执行会跳转到这里并继续执行
    console.log("很抱歉，数据有错误，我们会尝试再请求一次。");
    console.log(err.name);
    console.log(err.message);
}
```



这里我们将错误输出了，我们可以做更多的事情，例如新发送一个网络请求，或者将日志发送到记录日志的设备。



## 4 输出自定义的错误

有时候我们可能知道这里容易出问题，那么如何输出我们自定义的错误呢？

使用 throw 操作符， 语法：`throw <object error>`技术上我们可以将任何东西用作于error，甚至可以是一个原型类型数据，又或者是字符串或数字，但是最好使用对象，最好具有name和message属性的对象。 某种程度上和内建的Error对象保持兼容。

JS中有很多内建的标准 error 的构造器：Error，SyntaxError，ReferenceError，TypeError 等。我们也可以使用它们来创建 error 对象。例如：

```js
let error = new Error(message);
// 或
let error = new SyntaxError(message);
let error = new ReferenceError(message);
// ...
```

内建的error对象， name属性刚刚好就是构造器的名字，message 就是构造器的参数。

例如我们使用内建的错误对象，构造一个我们自己的错误：

```js
let json = '{ "age": 30 }'; // 不完整的数据

try {

    let user = JSON.parse(json); // <-- 没有 error

    if (!user.name) {
        throw new SyntaxError("数据不全：没有 name"); // (*)
    }

    console.log(user.name);

} catch (err) {
    console.log("JSON Error: " + err.message); // JSON Error: 数据不全：没有 name
}
```



## 5 再次抛出error

在上述的例子中 try...catch 用于捕获 数据不正确的error, 但是 try...代码块可能会继续抛出其他的错误。

```js
let json = '{ "age": 30 }'; // 不完整的数据

try {
    user = JSON.parse(json); // <-- 忘记在 user 前放置 "let"

    // ...
} catch (err) {
    console.log("JSON Error: " + err); // JSON Error: ReferenceError: user is not defined
    // (实际上并没有 JSON Error)
}
```

实际上，catch会捕获到所有来自于try...代码块的error，上面的代码中 错误已经不再是 json error 了， 是其他的错误，但是错误信息还是 JSON Error。

为了避免此类的问题，我们采用“重新抛出技术”，规则很简单：

catch 只处理他知道的error，并抛出其他的error。

再次抛出的技术可以理解为：

1. catch 捕获所有的error。
2. 在catch(err){...} 块中，我们对error对象err进行分析。
3. 如果我们不知道如何处理它，那么我们就throw err。

通常我们可以使用instanceif 操作符判断错误类型：

```js
try {
    user = { /*...*/ };
} catch (err) {
    if (err instanceof ReferenceError) {
        console.log('ReferenceError'); // 访问一个未定义（undefined）的变量产生了 "ReferenceError"
    }
}
```

我们还可以从err.name 属性中获取错误类型的类名，所有的原生的错误都有这个类型，另一种方式是读取err.constructor.name。

```js
let json = '{ "age": 30 }'; // 不完整的数据
try {

    let user = JSON.parse(json);

    if (!user.name) {
        throw new SyntaxError("数据不全：没有 name");
    }

    blabla(); // 预料之外的 error

    console.log(user.name);

} catch (err) {
	//     if (err.constructor.name == "SyntaxError") {
    if (err instanceof SyntaxError) {
        console.log("JSON Error: " + err.message);
    } else {
        throw err; // 再次抛出 (*)
    }

}
```



## 6 try…catch…finally

try...catch 还有一个代码结构， finally的代码所有的情况都会被执行，

```js
try {
   ... 尝试执行的代码 ...
} catch (err) {
   ... 处理 error ...
} finally {
   ... 总是会执行的代码 ...
}
```



一个例子：

```js
let num = +prompt("输入一个正整数？", 35)

let diff, result;

function fib(n) {
    if (n < 0 || Math.trunc(n) != n) {
        throw new Error("不能是负数，并且必须是整数。");
    }
    return n <= 1 ? n : fib(n - 1) + fib(n - 2);
}

let start = Date.now();

try {
    result = fib(num);
} catch (err) {
    result = 0;
} finally {
    diff = Date.now() - start;
}

console.log(result || "出现了 error");

console.log(`执行花费了 ${diff}ms`);
```



> Tips: 变量和 try...catch...finally 中的局部变量
>
> 
>
> 请注意，上面代码中的 result 和 diff 变量都是在 try...catch 之前 声明的。
>
> 否则，如果我们使用 let 在 try 块中声明变量，那么该变量将只在 try 块中可见。



```js
function func() {

    try {
        return 1;

    } catch (err) {
        /* ... */
    } finally {
        console.log('finally');
    }
}

console.log(func()); // 先执行 finally 中的 console.log，然后执行这个 console.log
```



没有catch...finally 结构也很有用，当我们不想在这处理error，但是需要确保我们启动的处理需要被完成。



## 7 全局的catch

在浏览器中我们可以讲一个函数赋值给特殊的 window.onerror 属性，该函数在发生未捕获的error时执行。

语法如下：

```js
window.onerror = function (message, url, line, col, error) {
    // ...
};
// message: error 信息。
// url :发生 error 的脚本的 URL。
// line，col: 发生 error 处的代码的行号和列号。
//  error:error 对象。
```



例如：

```html
<script>
    window.onerror = function (message, url, line, col, error) {
        alert(`${message}\n At ${line}:${col} of ${url}`);
    };

    function readData() {
        badFunc(); // 啊，出问题了！
    }

    readData();
</script>
```

全局错误处理程序的作用通常不是回复程序的方法，而是在程序发生错误的时候，将错误消息发送给开发者。



## 8 总结

try...catch 结构允许我们处理执行过程中出现的error, 从字面上拉看他允许尝试运行代码，并捕获其中可能发生的error。

error 对象包括以下属性：

1. name error名称的字符串。
2. message 人类可读的错误信息。
3. stack error发生时的调用栈。

如果需要error对象，也可以直接使用 try{...}catch {...}。

我们可以使用throw 抛出自己根据内建对象定义的错误对象。

再次抛出  throwing 是一种错误处理的常用模式。
