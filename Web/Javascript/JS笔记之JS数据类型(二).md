---
title: JS笔记之JS数据类型(二)
author: chaizz
date: 2023-1-29 16:14:32
tags: JavaScript
photo: ["https://tc.chaizz.com/ec55444c4a1211edac740242ac190002.png"]
---

​                    

<!--more-->



# JS笔记之JS数据类型(二)

## 1 字符串

在 JavaScript 中，文本数据被以字符串形式存储，单个字符没有单独的类型。 字符串是不可变的。

字符串的内部格式始终是 [UTF-16](https://en.wikipedia.org/wiki/UTF-16)，它不依赖于页面编码。

### 1.1 引号

在字符串中有三种引号，单引号和双引号基本相同。但是，反引号允许我们通过 `${…}` 将任何表达式嵌入到字符串中。 使用反引号允许字符串跨行。

反引号还允许我们在第一个反引号之前指定一个“模版函数”。语法是：`func`string``。函数 `func` 被自动调用，接收字符串和嵌入式表达式，并处理它们。你可以在 [docs](https://developer.mozilla.org/zh/docs/Web/JavaScript/Reference/Template_literals#Tagged_template_literals) 中阅读更多关于它们的信息。这叫做 “tagged templates”。此功能可以更轻松地将字符串包装到自定义模版或其他函数中，但这很少使用。

### 1.2 特殊字符

在上述的反引号能够换行，特殊字符也可以达到目的。

| 字符                                    | 描述                                                         |
| :-------------------------------------- | :----------------------------------------------------------- |
| `\n`                                    | 换行                                                         |
| `\r`                                    | 在 Windows 文本文件中，两个字符 `\r\n` 的组合代表一个换行。而在非 Windows 操作系统上，它就是 `\n`。这是历史原因造成的，大多数的 Windows 软件也理解 `\n`。 |
| `\'`, `\"`                              | 引号                                                         |
| `\\`                                    | 反斜线                                                       |
| `\t`                                    | 制表符                                                       |
| `\b`, `\f`, `\v`                        | 退格，换页，垂直标签 —— 为了兼容性，现在已经不使用了。       |
| `\xXX`                                  | 具有给定十六进制 Unicode `XX` 的 Unicode 字符，例如：`'\x7A'` 和 `'z'` 相同。 |
| `\uXXXX`                                | 以 UTF-16 编码的十六进制代码 `XXXX` 的 Unicode 字符，例如 `\u00A9` —— 是版权符号 `©` 的 Unicode。它必须正好是 4 个十六进制数字。 |
| `\u{X…XXXXXX}`（1 到 6 个十六进制字符） | 具有给定 UTF-32 编码的 Unicode 符号。一些罕见的字符用两个 Unicode 符号编码，占用 4 个字节。这样我们就可以插入长代码了。 |

```js
alert( "\u00A9" ); // ©
alert( "\u{20331}" ); // 佫，罕见的中国象形文字（长 Unicode）
alert( "\u{1F60D}" ); // 😍，笑脸符号（另一个长 Unicode）
```

反斜杠 `\` 在 JavaScript 中用于正确读取字符串，然后消失。内存中的字符串没有 `\`。



### 1.3 length 字符串长度

```js
console.log( `My\n`.length ); // 3  \n 是一个单独的“特殊”字符，所以长度确实是 3。
```

length 是一个属性。



### 1.4 访问字符

要获取在pos位置的一个字符，可以使用`[]` 或者调用`str.charAt(pos)`，第一个字符串位置从0开始。

```js
let str = `Hello`;

// 第一个字符
alert( str[0] ); // H
alert( str.charAt(0) ); // H

// 最后一个字符
alert( str[str.length - 1] ); // o
```

方括号是获取字符的一种现代化方法，而 `charAt` 是历史原因才存在的。

它们之间的唯一区别是，如果没有找到字符，`[]`返回 `undefined`，而 `charAt` 返回一个空字符串 ''

也可以使用for..of 遍历字符串

```js
for (let char of "Hello") {
  alert(char); // H,e,l,l,o（char 变为 "H"，然后是 "e"，然后是 "l" 等）
}
```



### 1.5 字符串常用方法

- **改变大小写**：[toLowerCase()](https://developer.mozilla.org/zh/docs/Web/JavaScript/Reference/Global_Objects/String/toLowerCase) 和 [toUpperCase()](https://developer.mozilla.org/zh/docs/Web/JavaScript/Reference/Global_Objects/String/toUpperCase)。

- **查找子字符串**：[str.indexOf(substr, pos)](https://zh.javascript.info/string#strindexof)，它从给定位置 `pos` 开始，在 `str` 中查找 `substr`，如果没有找到，则返回 `-1`，否则返回匹配成功的位置。 pos 允许我们从指定位置查找。**str.lastIndexOf(substr, pos)**：它从字符串的末尾开始搜索到开头，它会以相反的顺序列出这些事件。

  在if 中使用 indexOf ， 应该判断是否不等于 -1。

  ```js
  let str = "Widget with id";
  
  if (str.indexOf("Widget") != -1) {
      alert("We found it"); // 现在工作了！
  }
  ```

- 删除字符串前后的空格：`str.trim()`
- 重复字符串n次：`str.repeat(n)`



### 1.6 [按位（bitwise）NOT 技巧](https://zh.javascript.info/string#an-wei-bitwisenot-ji-qiao)

这里使用的一个老技巧是 [bitwise NOT](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Bitwise_NOT) `~` 运算符。它将数字转换为 32-bit 整数（如果存在小数部分，则删除小数部分），然后对其二进制表示形式中的所有位均取反。

实际上，这意味着一件很简单的事儿：对于 32-bit 整数，`~n` 等于 `-(n+1)`。

例如：

```javascript
alert( ~2 ); // -3，和 -(2+1) 相同
alert( ~1 ); // -2，和 -(1+1) 相同
alert( ~0 ); // -1，和 -(0+1) 相同
alert( ~-1 ); // 0，和 -(-1+1) 相同
```

正如我们看到这样，只有当 `n == -1` 时，`~n` 才为零（适用于任何 32-bit 带符号的整数 `n`）。

因此，仅当 `indexOf` 的结果不是 `-1` 时，检查 `if ( ~str.indexOf("...") )` 才为真。换句话说，当有匹配时。

人们用它来简写 `indexOf` 检查：

```js
let str = "Widget";

if (~str.indexOf("Widget")) {
  alert( 'Found it!' ); // 正常运行
}
```

只要记住：`if (~str.indexOf(...))` 读作 “if found”。

确切地说，由于 `~` 运算符将大数字截断为 32 位，因此存在给出 `0` 的其他数字，最小的数字是 `~4294967295=0`。这使得这种检查只有在字符串没有那么长的情况下才是正确的。

现在我们只会在旧的代码中看到这个技巧，因为现代 JavaScript 提供了 `.includes` 方法（见下文）。



### 1.7 includes，startsWith，endsWith

str.includes(substr, pos) 根据str 中是否包含 substr 来返回true或者false。

```js
// 检测匹配，但不需要它的位置
console.log( "Widget with id".includes("Widget") ); // true
console.log( "Hello".includes("Bye") ); // false
// 第二个参数也是查询的起始位置
console.log( "Widget".includes("id") ); // true
console.log( "Widget".includes("id", 3) ); // false, 从位置 3 开始没有 "id"
```

```js
alert( "Widget".startsWith("Wid") ); // true，"Widget" 以 "Wid" 开始
alert( "Widget".endsWith("get") ); // true，"Widget" 以 "get" 结束
```



### 1.8 获取子字符串

JS中有三种获取子字符串的方法， substring、substr、slice

str.slice(start [, end])：返回字符串从 `start` 到（但不包括）`end` 的部分。如果没有第二个参数，`slice` 会一直运行到字符串末尾。`start/end` 也有可能是负值。它们的意思是起始位置从字符串结尾计算。

```js
let str = "stringify";

// 从右边的第四个位置开始，在右边的第一个位置结束
alert( str.slice(-4, -1) ); // 'gif'
```

str.substring(start [, end])：返回字符串从 `start` 到（但不包括）`end` 的部分。这与 `slice` 几乎相同，但它允许 `start` 大于 `end`。不支持负参数。

```js
let str = "stringify";

// 这些对于 substring 是相同的
alert( str.substring(2, 6) ); // "ring"
alert( str.substring(6, 2) ); // "ring"

// ……但对 slice 是不同的：
alert( str.slice(2, 6) ); // "ring"（一样）
alert( str.slice(6, 2) ); // ""（空字符串）
```

str.substr(start [, length])：返回字符串从 `start` 开始的给定 `length` 的部分。

与以前的方法相比，这个允许我们指定 `length` 而不是**==结束位置==**：

```js
let str = "stringify";
alert( str.substr(2, 4) ); // 'ring'，从位置 2 开始，获取 4 个字符
```

具体使用哪一个？ 

>    它们都可用于获取子字符串。正式一点来讲，`substr` 有一个小缺点：它不是在 JavaScript 核心规范中描述的，而是在附录 B 中。附录 B 的内容主要是描述因历史原因而遗留下来的仅浏览器特性。因此，理论上非浏览器环境可能无法支持 `substr`，但实际上它在别的地方也都能用。
>
> 相较于其他两个变体，`slice` 稍微灵活一些，它允许以负值作为参数并且写法更简短。因此仅仅记住这三种方法中的 `slice` 就足够了。



### 1.9 比较字符串

字符串的比较按照字母的顺序逐字比较，但是有特殊情况：

1. 小写字母总是大于大写字母。`console.log( 'a' > 'Z' ); // true`

2. 带变音符号的字母存在“乱序”的情况。 

   ```js
   alert( 'Österreich' > 'Zealand' ); // true
   ```



为什么会出现乱序？

在JS中所有的字符串都使用 [UTF-16](https://en.wikipedia.org/wiki/UTF-16) 编码。即：每个字符都有对应的数字代码。有特殊的方法可以获取代码表示的字符，以及字符对应的代码。str.codePointAt(pos) 返回在 `pos` 位置的字符代码 :

```js
// 不同的字母有不同的代码
alert( "z".codePointAt(0) ); // 122
alert( "Z".codePointAt(0) ); // 90
```

也可以通过数字去创建字符

```js
alert( String.fromCodePoint(90) ); // Z
```

将65到220的数字代表的字符串全部打出来，我们可以看到先是大写字母，再是小写字母，明显a是大于Z的。结合上面的输出：

- 小写字母要比大写字母要大。
- 一些带有音标的特殊字母(Ö)比大写或者小写的字母都要大。

```js
let str = '';

for (let i = 65; i <= 220; i++) {
  str += String.fromCodePoint(i);
}
console.log( str );
// ABCDEFGHIJKLMNOPQRSTUVWXYZ[\]^_`abcdefghijklmnopqrstuvwxyz{|}~
// ¡¢£¤¥¦§¨©ª«¬­®¯°±²³´µ¶·¸¹º»¼½¾¿ÀÁÂÃÄÅÆÇÈÉÊËÌÍÎÏÐÑÒÓÔÕÖ×ØÙÚÛÜ
```



### 1.10 正确的比较字符串

现代的浏览器提供了一种特殊的方法来比较不同语言的字符串：`str.localComparse(str2)` 他会根据语言规则返回一个整数，这个整数能指示字符串str在排序顺序中排在str2的前面还是后面或者相同。

- str在str2的前面则返回负数。
- str在str2的后面则返回正数。
- 位置相等，则返回0。

```js
console.log( 'Österreich'.localeCompare('Zealand') ); // -1 代表 Österreich 小于 Zealand
```



## 2 总结

有三种类型的引号，反引号允许跨行显示并且可以使用${}在字符串中钳入表达式。

JS中的字符串使用的是UTF-16编码。

获取字符时使用：[], 获取子字符串使用slice和substring。

当根据语言比较字符串时使用`str.localcomparse(str2)`,否则则按照字符代码比较。





































