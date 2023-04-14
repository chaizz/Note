---
title: JS笔记之JS网络请求(一)
author: chaizz
date: 2023-4-14
tags: JavaScript
photo: ["https://tc.chaizz.com/ec55444c4a1211edac740242ac190002.png"]
---

​         

<!--more-->

JS笔记之JS网络请求(一)

fetch 

基本语法：

```js
// url: 要访问的连接
// options： 可选参数，method， headers 等
let promise = fetch(url, [options])
```

 没有options就是一个简单的get请求，下载url的连接， 浏览器会立即执行，并返回一个promise。

获取响应通常需要两个阶段：

**第一阶段**，当服务器发送了响应头，fetch返回的promise就是用内建的Response class 对象来对响应头对象进行解析。

在这个阶段，我们可以检查响应头，开检查http转态，以确定请求是否成功，当前还没有响应体，如果 fetch 无法建立一个 HTTP 请求，例如网络问题，亦或是请求的网址不存在，那么 promise 就会 reject。异常的 HTTP 状态，例如 404 或 500，不会导致出现 error。

我们可以在 response 的属性中看到 HTTP 状态：

- status —— HTTP 状态码，例如 200。
- ok —— 布尔值，如果 HTTP 状态码为 200-299，则为 true。



**第二阶段**，response body，我们需要使用一个其他的方法调用。response提供了多种不同额方法来读取response body。

- **`response.text()`** —— 读取 response，并以文本形式返回 response，
- **`response.json()`** —— 将 response 解析为 JSON 格式，
- **`response.formData()`** —— 以 `FormData` 对象（在 [下一章](https://zh.javascript.info/formdata) 有解释）的形式返回 response，
- **`response.blob()`** —— 以 [Blob](https://zh.javascript.info/blob)（具有类型的二进制数据）形式返回 response，
- **`response.arrayBuffer()`** —— 以 [ArrayBuffer](https://zh.javascript.info/arraybuffer-binary-arrays)（低级别的二进制数据）形式返回 response，
- 另外，`response.body` 是 [ReadableStream](https://streams.spec.whatwg.org/#rs-class) 对象，它允许你逐块读取 body。

>**重要：**
>
>我们只能选择一种读取 body 的方法。
>
>如果我们已经使用了 `response.text()` 方法来获取 response，那么如果再用 `response.json()`，则不会生效，因为 body 内容已经被处理过了。



响应头  header

Response header 位于 `response.headers` 中的一个类似于 Map 的 header 对象。它不是真正的 Map，但是它具有类似的方法，我们可以按名称（name）获取各个 header，或迭代它们：

```js
// 获取一个 header
console.log(response.headers.get('Content-Type')); // application/json; charset=utf-8

// 迭代所有 header
for (let [key, value] of response.headers) {
    console.log(`${key} = ${value}`);
}
```



Request header 

在请求头中设置header, 例如：

```js
let response = fetch(protectedUrl, {
    headers: {
        Authentication: "secret",
    },
});
```

还有一些我们无法设置header 为了安全，

设置POST请求

发送POST请求，我们需要在options中设置一系列方法，包括 method、header、body等。

- **`method`** —— HTTP 方法，例如 `POST`，

- `body`

  —— request body，其中之一：

  - 字符串（例如 JSON 编码的），
  - `FormData` 对象，以 `multipart/form-data` 形式发送数据，
  - `Blob`/`BufferSource` 发送二进制数据，
  - [URLSearchParams](https://zh.javascript.info/url)，以 `x-www-form-urlencoded` 编码形式发送数据，很少使用。

JSON 形式是最常用的。

请注意，如果请求的 `body` 是字符串，则 `Content-Type` 会默认设置为 `text/plain;charset=UTF-8`。

但是，当我们要发送 JSON 时，我们会使用 `headers` 选项来发送 `application/json`，这是 JSON 编码的数据的正确的 `Content-Type`。

```js
let user = {
    name: 'John',
    surname: 'Smith'
};

let response = await fetch('/article/fetch/post/user', {
    method: 'POST',
    headers: {
        'Content-Type': 'application/json;charset=utf-8'
    },
    body: JSON.stringify(user)
});

let result = await response.json();
console.log(result.message);
```



发送图片

我们 bolb 和bufferSource 来提交二进制数据，例如：

```js
async function submit() {
    
    let blob = await new Promise(resolve => canvasElem.toBlob(resolve, 'image/png'));
    let response = await fetch('/article/fetch/post/image', {
        method: 'POST',
        body: blob
    });

    // 服务器给出确认信息和图片大小作为响应
    let result = await response.json();
    console.log(result.message);
}
```



在这里我们没有手动设置header 的Content-Type 	因为 `Blob` 对象具有内建的类型（这里是 `image/png`，通过 `toBlob` 生成的）。对于 `Blob` 对象，这个类型就变成了 `Content-Type` 的值。



总结

典型的 fetch 请求由两个 `await` 调用组成：

```javascript
let response = await fetch(url, options); // 解析 response header
let result = await response.json(); // 将 body 读取为 json
```

或者以 promise 形式：

```javascript
fetch(url, options)
  .then(response => response.json())
  .then(result => /* process result */)
```



响应的属性：

- `response.status` —— response 的 HTTP 状态码，
- `response.ok` —— HTTP 状态码为 200-299，则为 `true`。
- `response.headers` —— 类似于 Map 的带有 HTTP header 的对象。

获取 response body 的方法：

- **`response.text()`** —— 读取 response，并以文本形式返回 response，
- **`response.json()`** —— 将 response 解析为 JSON 对象形式，
- **`response.formData()`** —— 以 `FormData` 对象（`multipart/form-data` 编码，参见下一章）的形式返回 response，
- **`response.blob()`** —— 以 [Blob](https://zh.javascript.info/blob)（具有类型的二进制数据）形式返回 response，
- **`response.arrayBuffer()`** —— 以 [ArrayBuffer](https://zh.javascript.info/arraybuffer-binary-arrays)（低级别的二进制数据）形式返回 response。

到目前为止我们了解到的 fetch 选项：

- `method` —— HTTP 方法，
- `headers` —— 具有 request header 的对象（不是所有 header 都是被允许的）
- `body` —— 要以 `string`，`FormData`，`BufferSource`，`Blob` 或 `UrlSearchParams` 对象的形式发送的数据（request body）。

