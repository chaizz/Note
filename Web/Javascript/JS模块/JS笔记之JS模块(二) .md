---
title: JS笔记之JS模块(二)
author: chaizz
date: 2023-3-31
tags: JavaScript
photo: ["https://tc.chaizz.com/ec55444c4a1211edac740242ac190002.png"]
---

​         

<!--more-->

JS笔记之JS模块(二)



在声明前导出

我们可以在声明表达式前使用export 来导出我们要声明的对象，可以是类、变量、还是函数，例如：

```js
// 导出数组
export let months = ['Jan', 'Feb', 'Mar', 'Apr', 'Aug', 'Sep', 'Oct', 'Nov', 'Dec'];

// 导出 const 声明的变量
export const MODULES_BECAME_STANDARD_YEAR = 2015;

// 导出类
export class User {
    constructor(name) {
        this.name = name;
    }
}
```

>**导出 class/function 后没有分号**
>
>注意，在类或者函数前的 `export` 不会让它们变成 [函数表达式](https://zh.javascript.info/function-expressions)。尽管被导出了，但它仍然是一个函数声明。大部分 JavaScript 样式指南都不建议在函数和类声明后使用分号。



导出与声明分开

也可以将导出和声明分开 【从技术上讲，我们也可以把 `export` 放在函数上面。】

```js
function sayHi(user) {
    alert(`Hello, ${user}!`);
}

function sayBye(user) {
    alert(`Bye, ${user}!`);
}

export { sayHi, sayBye }; // 导出变量列表
```



Import *

通常我们把要导入的东西列在花括号中，但是如果有很多的要导入的内容，我们可以首映`import * as <obj>` 将所有的内容倒入味一个对象，例如：

```js
// 📁 main.js
import * as say from './say.js';

say.sayHi('John');
say.sayBye('John');
```



通常我们要明确的列出我们要导入的对象。这是因为：

1. 现代的构建工具（[webpack](https://webpack.js.org/) 和其他工具）将模块打包到一起并对其进行优化，以加快加载速度并删除未使用的代码。比如说我们向我们的项目里添加一个第三方的库，say,js ，他有很的函数：

   ```js
   // 📁 say.js
   export function sayHi() { ... }
   export function sayBye() { ... }
   export function becomeSilent() { ... }
   ```

   那么现在我们的项目里面使用了一个say.js 中的一个函数，

   ```js
   // 📁 main.js
   import {sayHi} from './say.js';
   ```

   此时优化器就会检测到，我们项目中只是用了了一say.js中的函数，他就会将打包好的代码中删除那些未被使用的函数，从而使构建更小。这就是所谓的“摇树（tree-shaking）”。

2. 明确列出要导入的内容会使得名称较短：`sayHi()` 而不是 `say.sayHi()`。

3. 导入的显式列表可以更好地概述代码结构：使用的内容和位置。它使得代码支持重构，并且重构起来更容易。



import as 

使导入具有不同的名字，例如，简洁起见，我们将 `sayHi` 导入到局部变量 `hi`，将 `sayBye` 导入到 `bye`：

```js
// 📁 main.js
import {sayHi as hi, sayBye as bye} from './say.js';

hi('John'); // Hello, John!
bye('John'); // Bye, John!
```

export as 

导出也具有类似的语法。例如，我们将函数导出为 `hi` 和 `bye`。

```js
// 📁 say.js
...
export {sayHi as hi, sayBye as bye};
```

现在 `hi` 和 `bye` 是在外面使用时的正式名称：

```js
// 📁 main.js
import * as say from './say.js';

say.hi('John'); // Hello, John!
say.bye('John'); // Bye, John!
```



export default

在实际使用中，主要有两种模块。

- 包含库或者函数包的模块。
- 声明单个实体的模块，例如模块user.js仅导出class User

大部分情况，更倾向于使用第二种情况，因为这样每个文件都属于同一个模块。

模块提供了一个特殊的默认导出 `export default` 语法，以使“一个模块只做一件事”的方式看起来更好。

```js
// 📁 user.js
export default class User { // 只需要添加 "default" 即可
  constructor(name) {
    this.name = name;
  }
}
```

每个文件都应该只有一个export deafult，在将其导入的时候不需要使用花括号。

```js
// 📁 main.js
import User from './user.js'; // 不需要花括号 {User}，只需要写成 User 即可

new User('John');
```



所以默认的导出和普通的导出的别就是默认的导出不需要使用花括号。

| 命名的导出                | 默认的导出                        |
| :------------------------ | :-------------------------------- |
| `export class User {...}` | `export default class User {...}` |
| `import {User} from ...`  | `import User from ...`            |

在技术层面，一个文件既可以有默认的导出，和普通的导出，但是一般我们不这样用。、

由于每个文件最多只能有一个默认的导出，因此导出的实体可能没有名称。例如下面的这些导出都是正确的。

```js
export default class { // 没有类名
    constructor() { ... }
}

export default function (user) { // 没有函数名
    alert(`Hello, ${user}!`);
}

// 导出单个值，而不使用变量
export default ['Jan', 'Feb', 'Mar', 'Apr', 'Aug', 'Sep', 'Oct', 'Nov', 'Dec']

```

如果我们将所有东西 `*` 作为一个对象导入，那么 `default` 属性正是默认的导出：

```javascript
// 📁 main.js
import * as user from './user.js';

let User = user.default; // 默认的导出
new User('John');
```

对于默认的导出，我们可以再导入时起任意的名字：
```js
import User from './user.js'; // 有效
import MyUser from './user.js'; // 也有效
// 使用任何名称导入都没有问题
```

但是为了命名的统一，可以遵从这条规则：**即导入的变量应与文件名相对应。**例如：

```js
import User from './user.js';
import LoginForm from './loginForm.js';
import func from '/path/to/func.js';
```



重新导出

“重新导出（Re-export）”语法 `export ... from ...` 允许导入内容，并立即将其导出（可能是用的是其他的名字），就像这样：

```javascript
export {sayHi} from './say.js'; // 重新导出 sayHi

export {default as User} from './user.js'; // 重新导出 default
```

一个例字就是，我们开发的一个包，我们可能只希望用户从某个文件中导出这个对象，我们就可以使用 重新导出语法。

重新导出和默认导出。

重新导出时吗，那么默认导出就需要单独处理，假设我们有一个 `user.js` 脚本，其中写了 `export default class User`，并且我们想重新导出类 `User`：

可能会有两个问题：

- `export User from './user.js'` 无效。这会导致一个语法错误。要重新导出默认导出，我们必须明确写出 `export {default as User}`，就像上面的例子中那样。

- `export * from './user.js'` 重新导出只导出了命名的导出，但是忽略了默认的导出。

  ```js
  export * from './user.js'; // 重新导出命名的导出
  export {default} from './user.js'; // 重新导出默认的导出
  ```



总结：

- 导出：
  - 在声明一个 class/function/… 之前：
    - `export [default] class/function/variable ...`
  - 独立的导出：
    - `export {x [as y], ...}`.
  - 重新导出：
    - `export {x [as y], ...} from "module"`
    - `export * from "module"`（不会重新导出默认的导出）。
    - `export {default [as y]} from "module"`（重新导出默认的导出）。
- 导入
  - 导入命名的导出：
    - `import {x [as y], ...} from "module"`
  - 导入默认的导出：
    - `import x from "module"`
    - `import {default as x} from "module"`
  - 导入所有：
    - `import * as obj from "module"`
  - 导入模块（其代码，并运行），但不要将其任何导出赋值给变量：
    - `import "module"`

在实际开发中，导入通常位于文件的开头。

无效的导出导入语句：

```js
if (something) {
  import {sayHi} from "./say.js"; // Error: import must be at top level
}
```





