---
title: JS笔记之JS模块(一)
author: chaizz
date: 2023-3-30
tags: JavaScript
photo: ["https://tc.chaizz.com/ec55444c4a1211edac740242ac190002.png"]
---

​         

<!--more-->

JS笔记之JS模块(一)

随着我们的应用越来越大，我们想要将其拆分成多个文件，即所谓的“模块（module）”。一个模块可以包含用于特定目的的类或函数库。

但是当脚本变得越来越复杂，社区发明了许多的方法将代码组织到模块中去。使用特殊的库来加载模块。

什么是模块

模块就是一个文件，一个脚本。模块可以相互加载，使用export或者import来交换功能。

从一个模块调用另一个模块的函数

- `export` 关键字标记了可以从当前模块外部访问的变量和函数。
- `import` 关键字允许从其他模块导入功能。

例如：

在一个文件中有如下代码：

```js
// 📁 sayHi.js
export function sayHi(user) {
    return `Hello, ${user}!`;
}
```

然后另外一个文件导入了上面文件中的某个函数：

```js
// 📁 main.js
import { sayHi } from './sayHi.js';

console.log(sayHi); // function...
sayHi('John'); // Hello, John!
```

在浏览器中执行：（由于模块支持特殊的关键字和功能，因此我们必须通过使用 `<script type="module">` 特性来告诉浏览器，此脚本应该被当作模块（module）来对待。）

```html
<script type="module">
    import { sayHi } from "./009.js";

    document.body.innerHTML = sayHi("John");
</script>
```

> **模块只通过 HTTP(s) 工作，而非本地**
>
> 如果你尝试通过 `file://` 协议在本地打开一个网页，你会发现 `import/export` 指令不起作用。



模块的核心功能

始终使用 “use strict”

模块始终在严格模式下运行。例如，对一个未声明的变量赋值将产生错误（译注：在浏览器控制台可以看到 error 信息）。

```js
<script type="module">
    a = 5; // error
</script>
```



模块级作用域

每个模块都有自己的顶级作用域，换句话说就是一个模块的顶级作用域变量，和函数在其他的脚本中是不可见的，

在浏览器中，每个Html页面 中的每个`<script typer="module">`都是独立的顶级作用域。例如下面的情况两个 script标签彼此看不到自己的顶级变量：

```html
<script type="module">
    // 变量仅在这个 module script 内可见
    let user = "John";
</script>

<script type="module">
    console.log(user); // Error: user is not defined
</script>
```

>在浏览器中，我们可以通过将变量显式地分配给 `window` 的一个属性，使其成为窗口级别的全局变量。例如 `window.user = "John"`。
>
>这样所有脚本都会看到它，无论脚本是否带有 `type="module"`。
>
>也就是说，创建这种全局变量并不是一个好的方式。请尽量避免这样做。



模块代码仅在第一次导入时被解析

如果一个模块被导入到多个位置，name他的代码智慧执行一次， 即第一次被导入时，然后将其导出export的内容，提供给进一步的导入。**只执行一次会产生很重要的影响。**

看几个例子：

首先，如果执行一个模块中的代码会带来副作用（side-effect），例如显示一条消息，那么多次导入它只会触发一次显示 —— 即第一次：

```javascript
// 📁 alert.js
alert("Module is evaluated!");


// 在不同的文件中导入相同的模块

// 📁 1.js
import `./alert.js`; // Module is evaluated!

// 📁 2.js
import `./alert.js`; // (什么都不显示)
```

这里有一条规则：顶层模块代码应该用于初始化，创建模块特定的内部数据结构。如果我们需要多次调用某些东西 —— 我们应该将其以函数的形式导出，就像我们在上面使用 `sayHi` 那样。

一个比较复杂的例子：// 假设一个模块导出了一个对象。

```js
// 📁 admin.js
export let admin = {
    name: "John"
};
```

如果他被导入到其他的多个文件中，模块仅在第一次导入时被解析。并创建admin对象，然后将其传入到所有的导入。

所有的导入都只获得了一个唯一的admin对象。

```js
// 📁 1.js
import { admin } from './admin.js';
admin.name = "Pete";

// 📁 2.js
import { admin } from './admin.js';
alert(admin.name); // Pete

// 1.js 和 2.js 引用的是同一个 admin 对象
// 在 1.js 中对对象做的更改，在 2.js 中也是可见的
```

当在 `1.js` 中修改了导入的 `admin` 中的 `name` 属性时，我们在 `2.js` 中可以看到新的 `admin.name`。正式因为该模块只执行了一次，然后这个导出在各个导入之间。因此如果更改了admin对象在其他的导入中也会看到。



我们可以得到一个经典的模块使用方法：

模块可以提供通用的配置功能，例如身份验证，那么模块可以导出一个配置对象，期望外部代码可以对其赋值。

1. 模块导出一些配置方法，例如一个配置对象。
2. 在第一次导入时，我们对其进行初始化，写入其属性。可以在应用顶级脚本中进行此操作。
3. 进一步地导入使用模块。





import.meta

import.meta 对象包含关于当前模块的信息。

他的内容取决于器所在的环境，在浏览器环境中，它包含当前脚本的URL，或者他在Html中的话，则包含当前页面的URL。

```js
<script type="module">
    alert(import.meta.url); // 脚本的 URL
// 对于内联脚本来说，则是当前 HTML 页面的 URL
</script>
```



在一个模块中this是undefined

在一个模块中，顶级 `this` 是 undefined。

将其与非模块脚本进行比较会发现，非模块脚本的顶级 `this` 是全局对象：

```js
<script>
    alert(this); // window
</script>

<script type="module">
    alert(this); // undefined
</script>
```



浏览器的特定功能

模块脚本是延迟的

模块脚本总是被延迟的，与defer特性相比，对外部脚本和内联脚本的影响相同。也就是说：

- 下载外部模块脚本`<script type="module" src="...">` 不会阻塞html的处理，它们会与其他的资源并行加载。
- 模块脚本会等到html文档完全准备就绪然后才会运行。
- 脚本的顺序是固定的，在文档中排在前面的脚本先执行。

```js

<script type="module">
    alert(typeof button); // object：脚本可以“看见”下面的 button
    // 因为模块是被延迟的（deferred，所以模块脚本会在整个页面加载完成后才运行
</script>

相较于下面这个常规脚本：

<script>
    alert(typeof button); // button 为 undefined，脚本看不到下面的元素
    // 常规脚本会立即运行，常规脚本的运行是在在处理页面的其余部分之前进行的
</script>

<button id="button">Button</button>
```

当使用模块脚本时，我们应该知道 HTML 页面在加载时就会显示出来，在 HTML 页面加载完成后才会执行 JavaScript 模块，因此用户可能会在 JavaScript 应用程序准备好之前看到该页面。某些功能可能还无法使用。我们应该放置“加载指示器（loading indicator）”，或者以其他方式确保访问者不会因此而感到困惑。



Async 适用于内联脚本（inline script）

对于非模块脚本，async仅适用外部脚本，异步脚本会在准备好之后立即运行。独立于其他的脚本或者是html文档。

对于模块脚本它也适用于内联脚本。

例如，下面的内联脚本具有 `async` 特性，因此它不会等待任何东西。

它执行导入（fetch `./analytics.js`），并在导入完成时运行，即使 HTML 文档还未完成，或者其他脚本仍在等待处理中。

这对于不依赖任何其他东西的功能来说是非常棒的，例如计数器，广告，文档级事件监听器。

```html
<!-- 所有依赖都获取完成（analytics.js）然后脚本开始运行 -->
<!-- 不会等待 HTML 文档或者其他 <script> 标签 -->
<script async type="module">
    import { counter } from "./analytics.js";

    counter.count();
</script>
```



外部脚本

具有 `type="module"` 的外部脚本（external script）在两个方面有所不同：

1. 具有相同 `src` 的外部脚本仅运行一次：

   ```js
   <!-- 脚本 my.js 被加载完成（fetched）并只被运行一次 -->
   <script type="module" src="my.js"></script>
   <script type="module" src="my.js"></script>
   ```

2. 从另一个源（例如另一个网站）获取的外部脚本需要 [CORS](https://developer.mozilla.org/zh/docs/Web/HTTP/CORS) header，如我们在 [Fetch：跨源请求](https://zh.javascript.info/fetch-crossorigin) 一章中所讲的那样。换句话说，如果一个模块脚本是从另一个源获取的，则远程服务器必须提供表示允许获取的 header `Access-Control-Allow-Origin`。

   ```js
   <!-- another-site.com 必须提供 Access-Control-Allow-Origin -->
   <!-- 否则，脚本将无法执行 -->
   <script type="module" src="http://another-site.com/their.js"></script>
   ```

   

不允许裸模块

在浏览器中，`import` 必须给出相对或绝对的 URL 路径。没有任何路径的模块被称为“裸（bare）”模块。在 `import` 中不允许这种模块。例如下面的这个import是无效的。

```js
import {sayHi} from 'sayHi'; // Error，“裸”模块
// 模块必须有一个路径，例如 './sayHi.js' 或者其他任何路径
```

>某些环境，像 Node.js 或者打包工具（bundle tool）允许没有任何路径的裸模块，因为它们有自己的查找模块的方法和钩子（hook）来对它们进行微调。但是浏览器尚不支持裸模块。



兼容性  nomodule

旧时的浏览器不理解 `type="module"`。未知类型的脚本会被忽略。对此，我们可以使用 `nomodule` 特性来提供一个后备：

```js
<script type="module">
    alert("Runs in modern browsers");
</script>

<script nomodule>
    alert("Modern browsers know both type=module and nomodule, so skip this");
    alert(
        "Old browsers ignore script with unknown type=module, but execute this."
    );
</script>
```



构建工具

在实际开发中，浏览器模块很少被以“原始”形式进行使用。通常，我们会使用一些特殊工具，例如 [Webpack](https://webpack.js.org/)，将它们打包在一起，然后部署到生产环境的服务器。

使用打包工具的一个好处是 —— 它们可以更好地控制模块的解析方式，允许我们使用裸模块和更多的功能，例如 CSS/HTML 模块等。

构建工具做以下这些事儿：

1. 从一个打算放在 HTML 中的 `<script type="module">` “主”模块开始。
2. 分析它的依赖：它的导入，以及它的导入的导入等。
3. 使用所有模块构建一个文件（或者多个文件，这是可调的），并用打包函数（bundler function）替代原生的 `import` 调用，以使其正常工作。还支持像 HTML/CSS 模块等“特殊”的模块类型。
4. 在处理过程中，可能会应用其他转换和优化。
   - 删除无法访问的代码。
   - 删除未使用的导出（“tree-shaking”）。
   - 删除特定于开发的像 `console` 和 `debugger` 这样的语句。
   - 可以使用 [Babel](https://babeljs.io/) 将前沿的现代的 JavaScript 语法转换为具有类似功能的旧的 JavaScript 语法。
   - 压缩生成的文件（删除空格，用短的名字替换变量等）。

如果我们使用打包工具，那么脚本会被打包进一个单一文件（或者几个文件），在这些脚本中的 `import/export` 语句会被替换成特殊的打包函数（bundler function）。因此，最终打包好的脚本中不包含任何 `import/export`，它也不需要 `type="module"`，我们可以将其放入常规的 `<script>`：

```js
<!-- 假设我们从诸如 Webpack 这类的打包工具中获得了 "bundle.js" 脚本 -->
<script src="bundle.js"></script>
```



总结

1. 一个模块就是一个文件，浏览器需要使用`<script type=module>` 声明脚本，以便export/import可以工作。模块和常规的js脚本的差别
   1. 模块是延迟解析的。
   2. Async可用于内联脚本
   3. 要从另一个源或者网站需要使用CORS header。
   4. 重复的外部脚本会被忽略
2. 模块拥有自己的顶级作用域，并可以通过 `import/export` 交换功能。
3. 模块代码使用在严格模式下执行。
4. 模块代码只执行一次，导出近创建一次。然后会再导入之间共享。

当我们使用模块时，每个模块都会实现特定功能并将其导出。然后我们使用 import 将其直接导入到需要的地方即可。浏览器会自动加载并解析脚本。

在生产环境中，出于性能和其他原因，开发者经常使用诸如 Webpack 之类的打包工具将模块打包到一起。

在下一章里，我们将会看到更多关于模块的例子，以及如何进行导入/导出。