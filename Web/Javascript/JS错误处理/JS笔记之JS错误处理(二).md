---
title: JS笔记之JS错误处理(二).md
author: chaizz
date: 2023-3-21
tags: JavaScript
photo: ["https://origin.chaizz.com/ec55444c4a1211edac740242ac190002.png"]
---

​         

<!--more-->

# JS笔记之JS错误处理(二)

在我们开发的时候 经常需要我们自己定义的类来反应我们在程序中遇到的错误。比如在网络请求中我们是用HttpError，在操作数据库中我么使用DbError等等。

我们一般都会创建自己的自定义异常，他们都支持最基本的Error属性，例如 message、name、并且最好有stack。但是他们也有自己的属性，例如HttpError队形可能有一个statusCode属性。

JavaScript 允许将 throw 与任何参数一起使用，所以从技术上讲，我们自定义的 error 不需要从 Error 中继承。但是，如果我们继承，那么就可以使用 obj instanceof Error 来识别 error 对象。因此，最好继承它。

随着项目的增大，我们定义的异常，可能会越来越细分， 形成一个层次结构，例如：HttpTimeoutError 可能继承自 HttpError，等等。

## 1 扩展Error

例如当我们读一个一个用户的信息的json数据，有时候即使JSON的数据格式是正确的，但是数据却不一定是是我们想要的得到的数据，可能会缺少些必要的数据。

我们可以定义一个函数，来解析数据并且验证数据，如果没有得到我们要求的数据，那么就会抛出一个error，但是这个error不是 语法错误等，而是类似 ValidationError的错误对象。且是一个类，他可能继承自Error，且包含具体的错误信息等。例如：

```js
class ValidationError extends Error {
    // 调用了父类的 constructor。JavaScript 要求我们在子类的 constructor 中调用 super，所以这是必须的。
    // 父类的 constructor 设置了 message 属性。
    constructor(message) {
        super(message); // (1)
        // 父类的 constructor 还将 name 属性的值设置为了 "Error"，
        // 所以在这一行中，我们将其重置为了ValidationError
        this.name = "ValidationError"; // (2)
    }
}
```

然后我们在解析json中使用它：

```js
function readUser(json) {
    let user = JSON.parse(json);

    if (!user.age) {
        throw new ValidationError("No field: age");
    }
    if (!user.name) {
        throw new ValidationError("No field: name");
    }

    return user;
}

// try..catch 的工作示例

try {
    let user = readUser('{ "age": 25 }');
} catch (err) {
    if (err instanceof ValidationError) {
        console.log("Invalid data: " + err.message); // Invalid data: No field: name
    } else if (err instanceof SyntaxError) { // (*)
        console.log("JSON Syntax Error: " + err.message);
    } else {
        throw err; // 未知的 error，再次抛出 (**)
    }
}
```

在上面的try...cacth中既捕获了json解析的错误。也能捕获我们自定义的异常。在最后一中，，我们再次抛出了err。

## 2 深入继承

在数据中很多数据都有可能出错，我们可以为错误创建根据的异常对象。

```js
class ValidationError extends Error {
    constructor(message) {
        super(message);
        this.name = "ValidationError";
    }
}

class PropertyRequiredError extends ValidationError {
    constructor(property) {
        super("No property: " + property);
        this.name = "PropertyRequiredError";
        this.property = property;
    }
}

// 用法
function readUser(json) {
    let user = JSON.parse(json);

    if (!user.age) {
        throw new PropertyRequiredError("age");
    }
    if (!user.name) {
        throw new PropertyRequiredError("name");
    }

    return user;
}

// try..catch 的工作示例

try {
    let user = readUser('{ "age": 25 }');
} catch (err) {
    if (err instanceof ValidationError) {
        console.log("Invalid data: " + err.message); // Invalid data: No property: name
        console.log(err.name); // PropertyRequiredError
        console.log(err.property); // name
    } else if (err instanceof SyntaxError) {
        console.log("JSON Syntax Error: " + err.message);
    } else {
        throw err; // 未知 error，将其再次抛出
    }
}
```

这个类 PropertyRequiredError 使用起开很简单，我们只需要传递属性名， new PropertyRequiredError(property)， 人类刻度的message是 constructor 生成的。

> ​	Tips：在 PropertyRequiredError constructor 中的 this.name 是通过手动重新赋值的。这可能会变得有些乏味 —— 在每个自定义 error 类中都要进行 this.name = <class name> 赋值操作。可以通过创建自己的“基础错误（basic error）”类来避免这种情况，在基础错误类上进行 `this.name = this.constructor.name` 赋值，然后让我们的所有的异常类 都继承自这个基础错误类。

```js
class MyError extends Error {
    constructor(message) {
        super(message);
        this.name = this.constructor.name;
    }
}

class ValidationError extends MyError { }

class PropertyRequiredError extends ValidationError {
    constructor(property) {
        super("No property: " + property);
        this.property = property;
    }
}

// name 是对的
console.log(new PropertyRequiredError("field").name); // PropertyRequiredError
```



## 3 包装异常

我们扩展了ValidationError，PropertyRequiredError 未来可能会有其他的更多的类，try...catch.. 中使用了多个if else语句进行检查，这种方法并不是很优雅，那么有没有哟中比较高级的方法。答案是有的， 这种方法叫做包装异常。例如将上面的读取数据的例子进行包装：

1. 创建一个新的类ReadError，来表示一般的数据读取Error。
2. 函数readUser 将捕获内部发生的数据读取 error，例如 ValidationError 和 SyntaxError，并生成一个 ReadError 来进行替代。
3. 对象ReadError 会把原始的error 的引用，保存在其cause中。

经过上面的包装之后，再次调用 上面的 readUser 只需要检查 ReadError，而不必检查每种数据读取 error。并且，如果需要更多 error 细节，那么可以检查 readUser 的 cause 属性。例如：

```js
class ReadError extends Error {
    constructor(message, cause) {
        super(message);
        this.cause = cause;
        this.name = 'ReadError';
    }
}

class ValidationError extends Error { /*...*/ }
class PropertyRequiredError extends ValidationError { /* ... */ }

function validateUser(user) {
    if (!user.age) {
        throw new PropertyRequiredError("age");
    }

    if (!user.name) {
        throw new PropertyRequiredError("name");
    }
}

function readUser(json) {
    let user;

    try {
        user = JSON.parse(json);
    } catch (err) {
        if (err instanceof SyntaxError) {
            throw new ReadError("Syntax Error", err);
        } else {
            throw err;
        }
    }

    try {
        validateUser(user);
    } catch (err) {
        if (err instanceof ValidationError) {
            throw new ReadError("Validation Error", err);
        } else {
            throw err;
        }
    }

}

try {
    readUser('{bad json}');
} catch (e) {
    if (e instanceof ReadError) {
        console.log(e);
        // Original error: SyntaxError: Unexpected token b in JSON at position 1
        console.log("Original error: " + e.cause);
    } else {
        throw e;
    }
}
```



## 4 总结

1. 我们可以从Error和其他的内建error中进行继承，我们只需要注意name属性，以及调用super。
2. 我们可以使用 instanceof 来检查特定的 error。但有时我们有来自第三方库的 error 对象，并且在这儿没有简单的方法来获取它的类。那么可以将 name 属性用于这一类的检查。
3. 包装异常是一项广泛应用的技术：用于处理低级别异常并创建高级别 error 而不是各种低级别 error 的函数。在上面的示例中，低级别异常有时会成为该对象的属性，例如 err.cause，但这不是严格要求的。





































