---
title: JS笔记之DOM(六)
author: chaizz
date: 2023-4-13
tags: JavaScript
photo: ["https://tc.chaizz.com/ec55444c4a1211edac740242ac190002.png"]
---

​         

<!--more-->

# JS笔记之DOM(六)

表单（form）以及例如 `<input>` 的控件（control）元素有许多特殊的属性和事件。



## 1 导航：表单和元素
文档中的表单是特殊集合 document.forms 的成员。我们可以通过名字和编号来获取表单。

```js
document.forms.my; // name="my" 的表单
document.forms[0]; // 文档中的第一个表单
```

多个元素通过form.elements来获取，是一个集合。获取某个元素 ` let elem = form.elements.one; // <input name="one"> 元素`。如果有多个同名的元素：`let ageElems = form.elements.age; ageElems[0] `。

这些导航（navigation）属性并不依赖于标签的结构。所有的控件元素，无论它们在表单中有多深，都可以通过 form.elements 获取到。

>**更简短的表示方式：`form.name`**
>
>还有一个更简短的表示方式：我们可以通过 `form[index/name]` 来访问元素。
>
>换句话说，我们可以将 `form.elements.login` 写成 `form.login`。这也有效，但是会有一个小问题：如果我们访问一个元素，然后修改它的 `name`，之后它仍然可以被通过旧的 `name` 访问到（当然也能通过新的 `name` 访问）。



## 2 反向引用

对于任何元素，其对应的表单都可以通过 element.form 访问到。因此，表单引用了所有元素，元素也引用了表单。



## 3 input 和 textarea

我们可以通过 input.value（字符串）或 input.checked（布尔值）来访问复选框（checkbox）和单选按钮（radio button）中的 value。

>请注意，即使 `<textarea>...</textarea>` 将它们的 `value` 作为嵌套的 HTML 标签来保存，我们也绝不应该使用 `textarea.innerHTML` 来访问它。
>
>它仅存储最初在页面上的 HTML，而不是存储的当前 `value`。



## 4 select 和 option

一个 `<select>` 元素有 3 个重要的属性：

1. `select.options` —— `<option>` 的子元素的集合，
2. `select.value` —— 当前所选择的 `<option>` 的 **value**，
3. `select.selectedIndex` —— 当前所选择的 `<option>` 的**编号**。

它们提供了三种为 `<select>` 设置 `value` 的不同方式：

1. 找到对应的 `<option>` 元素（例如在 `select.options` 中），并将其 `option.selected` 设置为 `true`。
2. 如果我们知道新的值：将 `select.value` 设置为对应的新的值。
3. 如果我们知道新的选项的索引：将 `select.selectedIndex` 设置为对应 `<option>` 的编号。

```js
// 下面这三行做的都是同一件事
select.options[2].selected = true;
select.selectedIndex = 2;
select.value = "banana";
// 请注意：选项编号是从零开始的，所以编号 2 表示的是第三项
```



创建一个option元素

```js
option = new Option(text, value, defaultSelected, selected);

// text —— <option> 中的文本，
// value —— <option> 的 value，
// defaultSelected —— 如果为 true，那么 selected HTML-特性（attribute）就会被创建，
// selected —— 如果为 true，那么这个 <option> 就会被选中。
```

`defaultSelected` 和 `selected` 的区别是，`defaultSelected` 设置的是 HTML-特性（attribute），我们可以使用 `option.getAttribute('selected')` 来获得。而 `selected` 设置的是选项是否被选中。

在实际使用中，通常应该将**同时**将这两个值设置为 `true` 或 `false`。（或者，直接省略它们；两者都默认为 `false`。）

`<option>` 元素具有以下属性：

- `option.selected`  `<option>` 是否被选择。
- `option.index`  `<option>` 在其所属的 `<select>` 中的编号。
- `option.text`  `<option>` 的文本内容（可以被访问者看到）。



## 5 总结

表单导航：

- `document.forms`

  一个表单元素可以通过 `document.forms[name/index]` 访问到。

- `form.elements`

  表单元素可以通过 `form.elements[name/index]` 的方式访问，或者也可以使用 `form[name/index]`。`elements` 属性也适用于 `<fieldset>`。

- `element.form`

  元素通过 `form` 属性来引用它们所属的表单。

`value` 可以被通过 `input.value`，`textarea.value`，`select.value` 等来获取到。（对于单选按钮（radio button）和复选框（checkbox），可以使用 `input.checked` 来确定是否选择了一个值。

对于 `<select>`，我们可以通过索引 `select.selectedIndex` 来获取它的 `value`，也可以通过 `<option>` 集合 `select.options` 来获取它的 `value`。

这些是开始使用表单的基础。我们将在本教程中进一步介绍更多示例。

在下一章中，我们将介绍可能在任何元素上出现，但主要在表单上处理的 `focus` 和 `blur` 事件。









