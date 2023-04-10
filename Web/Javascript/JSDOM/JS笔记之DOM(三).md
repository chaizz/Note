---
title: JS笔记之DOM(三)
author: chaizz
date: 2023-4-3
tags: JavaScript
photo: ["https://tc.chaizz.com/ec55444c4a1211edac740242ac190002.png"]
---

​         

<!--more-->

JS笔记之DOM(三)

通过JS实时的创建DOM元素，是实时更新页面的关键。

创建元素

创建元素有两种方法：
```js
// 用给定的标签创建一个新 元素节点（element node）：
let div = document.createElement('div');

//用给定的文本创建一个 文本节点：
let textNode = document.createTextNode('Here I am');
```

将元素插入到HTML中

```js
// 将元素插入到body中。
document.body.append(div);

// 其他的插入方式
node.append(...nodes or strings) —— 在 node 末尾 插入节点或字符串，
node.prepend(...nodes or strings) —— 在 node 开头 插入节点或字符串，
node.before(...nodes or strings) —— 在 node 前面 插入节点或字符串，
node.after(...nodes or strings) —— 在 node 后面 插入节点或字符串，
node.replaceWith(...nodes or strings) —— 将 node 替换为给定的节点或字符串。
```

这些方法的参数可以是一个要插入的任意的 DOM 节点列表，或者文本字符串（会被自动转换成文本节点）。

insertAdjacentHTML/Text/Element

语法：`elem.insertAdjacentHTML(where, html)`。

该方法的第一个参数是代码字（code word），指定相对于 `elem` 的插入位置。必须为以下之一：

- `"beforebegin"` —— 将 `html` 插入到 `elem` 之前，
- `"afterbegin"` —— 将 `html` 插入到 `elem` 开头，
- `"beforeend"` —— 将 `html` 插入到 `elem` 末尾，
- `"afterend"` —— 将 `html` 插入到 `elem` 之后。

这个方法有两个兄弟：

- `elem.insertAdjacentText(where, text)` —— 语法一样，但是将 `text` 字符串“作为文本”插入而不是作为 HTML，
- `elem.insertAdjacentElement(where, elem)` —— 语法一样，但是插入的是一个元素。

它们的存在主要是为了使语法“统一”。实际上，大多数时候只使用 `insertAdjacentHTML`。因为对于元素和文本，我们有 `append/prepend/before/after` 方法 —— 它们也可以用于插入节点/文本片段，但写起来更短。



节点移除

语法：`node.remove()`

请注意：如果我们要将一个元素 **移动** 到另一个地方，则无需将其从原来的位置中删除。

**所有插入方法都会自动从旧位置删除该节点。**



克隆节点

语法：`elem.cloneNode(true)`

调用 `elem.cloneNode(true)` 来创建元素的一个“深”克隆 —— 具有所有特性（attribute）和子元素。如果我们调用 `elem.cloneNode(false)`，那克隆就不包括子元素。



DocumentFragment
DocumentFragment 是一个特殊的 DOM 节点，用作来传递节点列表的包装器（wrapper）。例如我们要创建一个li标签列表。例如：

```html
<ul id="ul"></ul>

<script>
    function getListContent() {
        let fragment = new DocumentFragment();

        for (let i = 1; i <= 3; i++) {
            let li = document.createElement("li");
            li.append(i);
            fragment.append(li);
        }

        return fragment;
    }

    ul.append(getListContent()); // (*)
</script>
```



样式和类

elem.className 将替换类中的整个字符串。

elem.classList 是一个特殊的对象，它具有 `add/remove/toggle` 单个类的方法。 例如：`body.classList.add("article")`。

- `elem.classList.add/remove(class)` —— 添加/移除类。
- `elem.classList.toggle(class)` —— 如果类不存在就添加类，存在就移除它。
- `elem.classList.contains(class)` —— 检查给定类，返回 `true/false`。



元素样式

ele.style 它对应于 `"style"` 特性（attribute）中所写的内容。`elem.style.width="100px"` 的效果等价于我们在 `style` 特性中有一个 `width:100px` 字符串。

对于多词（multi-word）属性，使用驼峰式 camelCase：

```javascript
background-color  => elem.style.backgroundColor
z-index           => elem.style.zIndex
border-left-width => elem.style.borderLeftWidth
```

前缀属性像 `-moz-border-radius` 和 `-webkit-border-radius` 这样的浏览器前缀属性，也遵循同样的规则：连字符 `-` 表示大写。

```js
button.style.MozBorderRadius = '5px';
button.style.WebkitBorderRadius = '5px';
```



重置样式属性

如果我们要分配一个样式属性，然后再删掉，例如style.display , 先进行设置值， 然后在将display的值设置为空字符串。

或者是使用elem.style.removeProperty('style property')。

**或者用 `style.cssText` 进行完全的重写**

通常，我们使用 `style.*` 来对各个样式属性进行赋值。我们不能像这样的 `div.style="color: red; width: 100px"` 设置完整的属性，因为 `div.style` 是一个对象，并且它是只读的。

想要以字符串的形式设置完整的样式，可以使用特殊属性 `style.cssText`：

```html
<div id="div">Button</div>

<script>
  // 我们可以在这里设置特殊的样式标记，例如 "important"
  div.style.cssText=`color: red !important;
    background-color: yellow;
    width: 100px;
    text-align: center;
  `;

  alert(div.style.cssText);
</script>
```

这个属性很少使用，因为这样的赋值会删除所有现有样式：它不是进行添加，而是替换它们

也可以通过设置一个特性（attribute）来实现同样的效果：`div.setAttribute('style', 'color: red...')`。

计算样式 getComputedStyle

语法：`getComputedStyle(element, [pseudo])`  `getComputedStyle` **需要完整的属性名**

element：需要被读取样式值的元素。

pseudo：伪元素（如果需要），例如 `::before`。空字符串或无参数则意味着元素本身。

结果是一个具有样式属性的对象，像 `elem.style`，但现在对于所有的 CSS 类来说都是如此。

**计算值和解析值**

在 [CSS](https://drafts.csswg.org/cssom/#resolved-values) 中有两个概念：

1. **计算 (computed)** 样式值是所有 CSS 规则和 CSS 继承都应用后的值，这是 CSS 级联（cascade）的结果。它看起来像 `height:1em` 或 `font-size:125%`。
2. **解析 (resolved)** 样式值是最终应用于元素的样式值。诸如 `1em` 或 `125%` 这样的值是相对的。浏览器将使用计算（computed）值，并使所有单位均为固定的，且为绝对单位，例如：`height:20px` 或 `font-size:16px`。对于几何属性，解析（resolved）值可能具有浮点，例如：`width:50.5px`。

很久以前，创建了 `getComputedStyle` 来获取计算（computed）值，但事实证明，解析（resolved）值要方便得多，标准也因此发生了变化。

所以，现在 `getComputedStyle` 实际上返回的是属性的解析值（resolved）。

>Tips:**应用于 `:visited` 链接的样式被隐藏了！**
>
>可以使用 CSS 伪类 `:visited` 对被访问过的链接进行着色。
>
>但 `getComputedStyle` 没有给出访问该颜色的方式，因为如果允许的话，任意页面都可以通过在页面上创建它，并通过检查样式来确定用户是否访问了某链接。
>
>JavaScript 看不到 `:visited` 所应用的样式。此外，CSS 中也有一个限制，即禁止在 `:visited` 中应用更改几何形状的样式。这是为了确保一个不好的页面无法检测链接是否被访问，进而窥探隐私。





总结：

- 创建新节点的方法：

  - `document.createElement(tag)` —— 用给定的标签创建一个元素节点，
  - `document.createTextNode(value)` —— 创建一个文本节点（很少使用），
  - `elem.cloneNode(deep)` —— 克隆元素，如果 `deep==true` 则与其后代一起克隆。

- 插入和移除节点的方法：

  - `node.append(...nodes or strings)` —— 在 `node` 末尾插入，
  - `node.prepend(...nodes or strings)` —— 在 `node` 开头插入，
  - `node.before(...nodes or strings)` —— 在 `node` 之前插入，
  - `node.after(...nodes or strings)` —— 在 `node` 之后插入，
  - `node.replaceWith(...nodes or strings)` —— 替换 `node`。
  - `node.remove()` —— 移除 `node`。

  文本字符串被“作为文本”插入。

- 这里还有“旧式”的方法：

  - `parent.appendChild(node)`
  - `parent.insertBefore(node, nextSibling)`
  - `parent.removeChild(node)`
  - `parent.replaceChild(newElem, node)`

  这些方法都返回 `node`。

- 在 `html` 中给定一些 HTML，`elem.insertAdjacentHTML(where, html)` 会根据 `where` 的值来插入它：

  - `"beforebegin"` —— 将 `html` 插入到 `elem` 前面，
  - `"afterbegin"` —— 将 `html` 插入到 `elem` 的开头，
  - `"beforeend"` —— 将 `html` 插入到 `elem` 的末尾，
  - `"afterend"` —— 将 `html` 插入到 `elem` 后面。

另外，还有类似的方法，`elem.insertAdjacentText` 和 `elem.insertAdjacentElement`，它们会插入文本字符串和元素，但很少使用。

- 要在页面加载完成之前将 HTML 附加到页面：

  - `document.write(html)`

  页面加载完成后，这样的调用将会擦除文档。多见于旧脚本。





要管理 class，有两个 DOM 属性：

- `className` —— 字符串值，可以很好地管理整个类的集合。
- `classList` —— 具有 `add/remove/toggle/contains` 方法的对象，可以很好地支持单个类。

要改变样式：

- `style` 属性是具有驼峰（camelCased）样式的对象。对其进行读取和修改与修改 `"style"` 特性（attribute）中的各个属性具有相同的效果。要了解如何应用 `important` 和其他特殊内容 —— 在 [MDN](https://developer.mozilla.org/zh/docs/Web/API/CSSStyleDeclaration) 中有一个方法列表。
- `style.cssText` 属性对应于整个 `"style"` 特性（attribute），即完整的样式字符串。

要读取已解析的（resolved）样式（对于所有类，在应用所有 CSS 并计算最终值之后）：

- `getComputedStyle(elem, [pseudo])` 返回与 `style` 对象类似的，且包含了所有类的对象。只读。
