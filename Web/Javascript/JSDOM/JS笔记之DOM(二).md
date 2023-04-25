---
title: JS笔记之DOM(二)
author: chaizz
date: 2023-4-3
tags: JavaScript
photo: ["https://origin.chaizz.com/ec55444c4a1211edac740242ac190002.png"]
---

​         

<!--more-->

# JS笔记之DOM(二)

## 1 DOM属性

DOM元素本身有很多自己的属性，但是我们也可以自己添加自定义的属性。例如将指定一个对象，或者设置一个函数之类的。

```js
document.body.myData = {
    name: 'Caesar',
    title: 'Imperator'
};
console.log(document.body.myData.title); // Imperator

document.body.sayTagName = function () {
    console.log(this.tagName);
};
document.body.sayTagName(); // BODY（这个方法中的 "this" 的值是 document.body）
```

同样的我们还可以修改内建属性的原型，添加一个自定义方法。

```js
Element.prototype.sayHi = function () {
    console.log(`Hello, I'm ${this.tagName}`);
};

document.documentElement.sayHi(); // Hello, I'm HTML
document.body.sayHi(); // Hello, I'm BODY
```

所以DOM属性和方法就像常规的JS对象一样，可以有很多值，且是大小写敏感的。



## 2 HTML标签特性

当浏览器解析 HTML 文本，并根据标签创建 DOM 对象时，浏览器会辨别 **标准的** 特性并以此创建 DOM 属性。例如：当一个元素有 `id` 或其他 **标准的** 特性，那么就会生成对应的 DOM 属性。但是非 **标准的** 特性则不会。

```html
<body id="test" something="non-standard">
    <script>
        alert(document.body.id); // test
        // 非标准的特性没有获得对应的属性
        alert(document.body.something); // undefined
    </script>
</body>
```

当一个元素是标准的特性，我们可以使用`标签.属性`来获取，但是如果不是标准的特性，如何获取呢？

JS提供了一下的一些方法来获取特性，既可以获取标准特性，也可以获取非标准特性：

- `elem.hasAttribute(name)` —— 检查特性是否存在。
- `elem.getAttribute(name)` —— 获取这个特性值。
- `elem.setAttribute(name, value)` —— 设置这个特性值。
- `elem.removeAttribute(name)` —— 移除这个特性。

我们也可以使用 `elem.attributes` 读取所有特性：属于内建 [Attr](https://dom.spec.whatwg.org/#attr) 类的对象的集合，具有 `name` 和 `value` 属性。例如获取一个非标准特性：

```html
<body something="non-standard">
    <script>
        alert(document.body.getAttribute("something")); // non-standard
    </script>
</body>
```

HTML的特性有几个特点：

- 对大小写不敏感，即大写小写都是一样的。
- 特性的值总是字符串类型的。



## 3 属性特性同步

当一个标准的特性被改变，对应的属性也会自动更新，（除了几个特例）反之亦然。例如input标签的text特性：

```html
<input />
<script>
    let input = document.querySelector("input");
    // 特性 => 属性
    input.setAttribute("value", "text");
    alert(input.value); // text

    // 这个操作无效，属性 => 特性
    input.value = "newValue";
    alert(input.getAttribute("value")); // text（没有被更新！）
</script>
```



## 4 DOM属性是多类型的

DOM属性不总是字符串类型的，。例如，`input.checked` 属性（对于 checkbox 的）是布尔型的。还有就是style的特性是字符串类型的，但是他的属性是对象类型。

```html
<div id="div" style="color: red; font-size: 120%">Hello</div>
<script>
    // 字符串
    alert(div.getAttribute("style")); // color:red;font-size:120%
    // 对象
    alert(div.style); // [object CSSStyleDeclaration]
    alert(div.style.color); // red
</script>
```

有一种非常少见的情况，即使一个 DOM 属性是字符串类型的，但它可能和 HTML 特性也是不同的。例如，`href` DOM 属性一直是一个 **完整的** URL，即使该特性包含一个相对路径或者包含一个 `#hash`。

如果我们需要 `href` 特性的值，或者其他与 HTML 中所写的完全相同的特性，则可以使用 `getAttribute`。



## 5 非标准的特性 dataset

非标准的特性，长长用于将html的内容，传递到 JS，或者用于为 JavaScript “标记” HTML 元素。

```html
<!-- 标记这个 div 以在这显示 "name" -->
<div show-info="name"></div>
<!-- 标记这个 div 以在这显示 "age" -->
<div show-info="age"></div>

<script>
    // 这段代码找到带有标记的元素，并显示需要的内容
    let user = {
        name: "Pete",
        age: 25,
    };

    for (let div of document.querySelectorAll("[show-info]")) {
        // 在字段中插入相应的信息
        let field = div.getAttribute("show-info");
        div.innerHTML = user[field]; // 首先 "name" 变为 Pete，然后 "age" 变为 25
    }
</script>
```

也可以设置元素的样式：

```html
<style>
    /* 样式依赖于自定义特性 "order-state" */
    .order[order-state="new"] {
        color: green;
    }
    .order[order-state="pending"] {
        color: blue;
    }
    .order[order-state="canceled"] {
        color: red;
    }
</style>

<div class="order" order-state="new">A new order.</div>
<div class="order" order-state="pending">A pending order.</div>
<div class="order" order-state="canceled">A canceled order.</div>
```



其中自定义特性也会有一定的问题，比如当我们使用了一些自定义特性，但是后来这些特性被添加到标准他特性中，就会产生冲突的问题。为了避免冲突，存在 [data-*](https://html.spec.whatwg.org/#embedding-custom-non-visible-data-with-the-data-*-attributes) 特性。

**所有以 “data-” 开头的特性均被保留供程序员使用。它们可在 `dataset` 属性中使用。**

例如，如果一个 `elem` 有一个名为 `"data-about"` 的特性，那么可以通过 `elem.dataset.about` 取到它。

```html
<body data-about="Elephants">
    <script>
        alert(document.body.dataset.about); // Elephants
    </script>
</body>
```

若果是多个词的可以改为驼峰调用：像 `data-order-state` 这样的可以以驼峰式进行调用：`dataset.orderState`。

例如上面额的一个例字的改版：

```html
<style>
    .order[data-order-state="new"] {
        color: green;
    }

    .order[data-order-state="pending"] {
        color: blue;
    }

    .order[data-order-state="canceled"] {
        color: red;
    }
</style>

<div id="order" class="order" data-order-state="new">A new order.</div>

<script>
    // 读取
    alert(order.dataset.orderState); // new

    // 修改
    order.dataset.orderState = "pending"; // (*)
</script>
```

使用 `data-*` 特性是一种合法且安全的传递自定义数据的方式。



## 6 总结

|      | 属性                                   | 特性                         |
| :--- | :------------------------------------- | ---------------------------- |
| 定义 | DOM 对象中的内容                       | 写在 HTML 中的内容           |
| 类型 | 任何值，标准的属性具有规范中描述的类型 | 字符串                       |
| 名字 | 名字（name）是大小写敏感的             | 名字（name）是大小写不敏感的 |

操作特性的方法：

- `elem.hasAttribute(name)` —— 检查是否存在这个特性。
- `elem.getAttribute(name)` —— 获取这个特性值。
- `elem.setAttribute(name, value)` —— 设置这个特性值。
- `elem.removeAttribute(name)` —— 移除这个特性。
- `elem.attributes` —— 所有特性的集合。

在大多数情况下，最好使用 DOM 属性。仅当 DOM 属性无法满足开发需求，并且我们真的需要特性时，才使用特性，例如：

- 我们需要一个非标准的特性。但是如果它以 `data-` 开头，那么我们应该使用 `dataset`。
- 我们想要读取 HTML 中“所写的”值。对应的 DOM 属性可能不同，例如 `href` 属性一直是一个 **完整的** URL，但是我们想要的是“原始的”值。
