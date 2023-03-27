---
title: js笔记之Promise(四)
author: chaizz
date: 2023-3-27
tags: JavaScript
photo: ["https://tc.chaizz.com/ec55444c4a1211edac740242ac190002.png"]
---

​          

<!--more-->

# js笔记之Promise(四)



## 1 Promisification

Promisification 它指将一个接受回调的函数转换为一个返回 promise 的函数。由于许多的函数都是基于回调的，因此在实际的开发中经常会遇到这种转化。一个简单的例子:

```js
function loadScript(src, callback) {
    let script = document.createElement('script');
    script.src = src;

    script.onload = () => callback(null, script);
    script.onerror = () => callback(new Error(`Script load error for ${src}`));

    document.head.append(script);
}

  // 用法：
  // loadScript('path/script.js', (err, script) => {...})
```

上面的例子 通过给定的src加载脚本，然后再出现错误是调用callback(err)，或者在加载成功时，调用callback(null，script) 。

接下来我们将这个函数promise化。

我们要做的就是将这个函数返回一个promise,而不是使用回调。换句话就是我们只传入src, 然后再函数中return一个promise。加载成功时该 promise 将以 `script` 为结果 resolve，否则将以出现的 error 为结果 reject。

```js
function loadScriptPromise(src) {
    return new Promise((resolve, reject) => {
        loadScript(src, (err, script) => {
            if (err) reject(err);
            else resolve(script)
        })
    })
}
```

在实际开发中，我们可能需要 promise 化很多函数，所以使用一个 helper（辅助函数）很有意义。

我们将其称为 `promisify(f)`：它接受一个需要被 promise 化的函数 `f`，并返回一个包装（wrapper）函数。

```javascript
// promisify(f, true) 来获取结果数组
function promisify(f, manyArgs = false) {
    return function (...args) {
        return new Promise((resolve, reject) => {
            function callback(err, ...results) { // 我们自定义的 f 的回调
                if (err) {
                    reject(err);
                } else {
                    // 如果 manyArgs 被指定，则使用所有回调的结果 resolve
                    resolve(manyArgs ? results : results[0]);
                }
            }

            args.push(callback);

            f.call(this, ...args);
        });
    };
}

// 用法：
f = promisify(f, true);
f(...).then(arrayOfResults => ..., err => ...);
```
