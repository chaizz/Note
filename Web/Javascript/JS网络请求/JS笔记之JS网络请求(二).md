---
title: JS笔记之JS网络请求(二)
author: chaizz
date: 2023-4-14
tags: JavaScript
photo: ["https://tc.chaizz.com/ec55444c4a1211edac740242ac190002.png"]
---

​         

<!--more-->

JS笔记之JS网络请求(二)

FormData

```javascript
let formData = new FormData([form]);
```

fetch 可以接受一个formData对象作为body，并且他的header包括ontent-Type: multipart/form-data。

dormData方法

我们可以使用以下方法修改 `FormData` 中的字段：

- `formData.append(name, value)` —— 添加具有给定 `name` 和 `value` 的表单字段，
- `formData.append(name, blob, fileName)` —— 添加一个字段，就像它是 `<input type="file">`，第三个参数 `fileName` 设置文件名（而不是表单字段名），因为它是用户文件系统中文件的名称，
- `formData.delete(name)` —— 移除带有给定 `name` 的字段，
- `formData.get(name)` —— 获取带有给定 `name` 的字段值，
- `formData.has(name)` —— 如果存在带有给定 `name` 的字段，则返回 `true`，否则返回 `false`。

一个表单可以包含多个具有相同name的字段， 所以多次调用append会添加多个具有相同名称的字段。还有一个set方法，他会移除具有name名称的字段，并重新添加。

- `formData.set(name, value)`，
- `formData.set(name, blob, fileName)`。

可以使用 for...of 来循环formData字段。



发送带有文件的表单字段

表单始终以 `Content-Type: multipart/form-data` 来发送数据，这个编码允许发送文件。因此 `<input type="file">` 字段也能被发送，类似于普通的表单提交。例如：

```js
formElem.onsubmit = async (e) => {
    e.preventDefault();

    let response = await fetch('/article/formdata/post/user-avatar', {
        method: 'POST',
        body: new FormData(formElem)
    });

    let result = await response.json();

    alert(result.message);
};
```



发送具有 Blob 数据的表单

一个核心的语法就是 使用表单的append 方法 添加 blob 对象。

```js
formData.append("image", imageBlob, "image.png");
```



fetch 终止

当我们需要终止一个fetch时，我们可以使：AbortController。它不仅可以中止 fetch，还可以中止其他异步任务。

基本语法：

```js
let controller = new AbortController();
// 它具有单个方法 abort()和单个属性 signal()
```

当 `abort()` 被调用时：

- `controller.signal` 就会触发 `abort` 事件。
- `controller.signal.aborted` 属性变为 `true`。

通常，我们需要处理两部分：

1. 一部分是通过在 `controller.signal` 上添加一个监听器，来执行可取消操作。
2. 另一部分是触发取消：在需要的时候调用 `controller.abort()`。



AbortController 和 fetch 结合使用

想要终止fetch，需要将 AbortController 的 signal 属性作为 fetch 的一个可选参数（option）进行传递：fetch 方法会监听 signal 上的 abort 事件。所以要终止fetch只需要调用controller.abort();

当一个 fetch 被中止，它的 promise 就会以一个 error AbortError reject，因此我们应该对其进行处理，例如在 try..catch 中。

一个完整的例子 ： 实现的效果为 一秒后终止请求

```js
// 1、构建一个 controller
let controller = new AbortController();
// 3、  controller.abort()调用 abort 执行中止。
setTimeout(() => controller.abort(), 1000);

// 4、终止后，fetch 会得到一个 AbortError, 所以需要使用try... catch 捕获
try {
    let response = await fetch('/article/fetch-abort/demo/hang', {
        // 2、将controller的signal 添加到ftech的options中
        signal: controller.signal
    });
} catch (err) {
    if (err.name == 'AbortError') { // handle abort()
        console.log("Aborted!");
    } else {
        throw err;
    }
```



AbortController 是可伸缩的 同时可以终止多个异步任务。如果我们有自己的不是fetch的异步任务，我们同样也可以使用单个AbortController来终止我们的任务，只需要监听其 `abort` 事件。

```js
let controller = new AbortController();

let ourJob = new Promise((resolve, reject) => { 
  // 在任务中 监听 abort 事件。
  controller.signal.addEventListener('abort', reject);
});
```



总结

- `AbortController` 是一个简单的对象，当 `abort()` 方法被调用时，会在自身的 `signal` 属性上生成 `abort` 事件（并将 `signal.aborted` 设置为 `true`）。
- `fetch` 与之集成：我们将 `signal` 属性作为可选参数（option）进行传递，之后 `fetch` 会监听它，因此它能够中止 `fetch`。
- 我们可以在我们的代码中使用 `AbortController`。“调用 `abort()`” → “监听 `abort` 事件”交互简单且通用。即使没有 `fetch`，我们也可以使用它。







