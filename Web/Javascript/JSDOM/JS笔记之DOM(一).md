---
title: JS笔记之DOM(一)
author: chaizz
date: 2023-4-3
tags: JavaScript
photo: ["https://tc.chaizz.com/ec55444c4a1211edac740242ac190002.png"]
---

​         

<!--more-->

# JS笔记之DOM(一)

## 1 遍历DOM

childNodes：获取DOM的所有子节点。childNodes得到的结果看起来像一个数组，但是他并不是一个数组，而是一个集合，一个类数组的可迭代对象。

他可迭代但是无法使用数组的方法，我们可以使用Array.from，使其成为一个真数组。

DOM集合的特点：

1. 是只读的`childNodes[i] = ...`的操作来替换一个子节点。
2. DOM集合是实时的，如果我们保留一个对 `elem.childNodes` 的引用，然后向 DOM 中添加/移除节点，那么这些节点的更新会自动出现在集合中。
3. 可以使用for..of对集合进行迭代。但是不建议使用 `for..in` 来迭代集合，for..in 循环遍历的是所有可枚举的（enumerable）属性。集合还有一些“额外的”很少被用到的属性，通常这些属性也是我们不期望得到的。

## 2 父节点的一个注意项

parentElement：属性返回的是“元素类型”的父节点。parentNode返回的是“任何类型”的父节点。

```js
alert( document.documentElement.parentNode ); // document
alert( document.documentElement.parentElement ); // null
```

因为根节点 `document.documentElement`（`<html>`）的父节点是 `document`。但 `document` 不是一个元素节点，所以 `parentNode` 返回了 `document`，但 `parentElement` 返回的是 `null`。

当我们想从任意节点 `elem` 到 `<html>` 而不是到 `document` 时，这个细节可能很有用：

```javascript
while(elem = elem.parentElement) { // 向上，直到 <html>
  alert( elem );
}
```

## 3 使用ID获取元素

```html
<div id="elem">
  <div id="elem-content">Element</div>
</div>

<script>
  // elem 是对带有 id="elem" 的 DOM 元素的引用
  elem.style.background = 'red';

  // id="elem-content" 内有连字符，所以它不能成为一个变量
  // ...但是我们可以通过使用方括号 window['elem-content'] 来访问它
</script>
```



## 4 搜索DOM

[elem.matches(css)](https://dom.spec.whatwg.org/#dom-element-matches) 不会查找任何内容，它只会检查 `elem` 是否与给定的 CSS 选择器匹配。它返回 `true` 或 `false`。

## 5 实时的集合

所有的 `"getElementsBy*"` 方法都会返回一个 **实时的（live）** 集合。这样的集合始终反映的是文档的当前状态，并且在文档发生更改时会“自动更新”。

`querySelectorAll` 返回的是一个 **静态的** 集合。就像元素的固定数组。

## 6 节点的属性

不同的 DOM 节点可能有不同的属性。

nodeName 和 tagName 读取元素的标签名，`tagName` 属性仅适用于 `Element` 节点。nodeName是为任意Node定义的：对于元素，它的意义与 `tagName` 相同。对于其他节点类型（text，comment 等），它拥有一个对应节点类型的字符串。

>标签名称始终是大写的，除非是在 XML 模式下**
>
>浏览器有两种处理文档（document）的模式：HTML 和 XML。通常，HTML 模式用于网页。只有在浏览器接收到带有 `Content-Type: application/xml+xhtml` header 的 XML-document 时，XML 模式才会被启用。
>
>在 HTML 模式下，`tagName/nodeName` 始终是大写的：它是 `BODY`，而不是 `<body>` 或 `<BoDy>`。
>
>在 XML 模式中，大小写保持为“原样”。如今，XML 模式很少被使用。

innerHTML：将元素中的 HTML 获取为字符串形式，是更改页面最有效的方法之一。

**如果 `innerHTML` 将一个 `<script>` 标签插入到 document 中 —— 它会成为 HTML 的一部分，但是不会执行。**

> 小心：“innerHTML+=” 会进行完全重写，因为内容已“归零”并从头开始重写，因此所有的图片和其他资源都将重写加载。

** `outerHTML` 不会改变元素。而是在 DOM 中替换它。**

获取文本节点的内容，可以使用nodeValue 和 data， 但是data更常用。

textContent 获取元素的纯文本。**它允许以“安全方式”写入文本。**



## 6 总结

有 6 种主要的方法，可以在 DOM 中搜索元素节点：

| 方法名                   | 搜索方式     | 可以在元素上调用？ | 实时的？ |
| ------------------------ | ------------ | ------------------ | -------- |
| `querySelector`          | CSS-selector | ✔                  | -        |
| `querySelectorAll`       | CSS-selector | ✔                  | -        |
| `getElementById`         | `id`         | -                  | -        |
| `getElementsByName`      | `name`       | -                  | ✔        |
| `getElementsByTagName`   | tag or `'*'` | ✔                  | ✔        |
| `getElementsByClassName` | class        | ✔                  | ✔        |

目前为止，最常用的是 `querySelector` 和 `querySelectorAll`，但是 `getElement(s)By*` 可能会偶尔有用，或者可以在旧脚本中找到。

此外：

- `elem.matches(css)` 用于检查 `elem` 与给定的 CSS 选择器是否匹配。
- `elem.closest(css)` 用于查找与给定 CSS 选择器相匹配的最近的祖先。`elem` 本身也会被检查。

让我们在这里提一下另一种用来检查子级与父级之间关系的方法，因为它有时很有用：

- 如果 `elemB` 在 `elemA` 内（`elemA` 的后代）或者 `elemA==elemB`，`elemA.contains(elemB)` 将返回 true。