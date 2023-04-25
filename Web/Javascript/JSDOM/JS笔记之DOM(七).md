---
title: JS笔记之DOM(七)
author: chaizz
date: 2023-4-14
tags: JavaScript
photo: ["https://origin.chaizz.com/ec55444c4a1211edac740242ac190002.png"]
---

​         

<!--more-->

JS笔记之DOM(七)



当用户点击某个元素或者使用Tab移动到某个元素上时，元素会获得聚焦（focus），HTML有一个特向 autofocus可以让网页加载时默认落在一个元素上。元素被聚焦通常意味着要接受数据。

失去焦点（blur）意味着用户点击了其他的地方，可能代表输入已经完成。

当元素聚焦时，会触发focus事件，失去焦点时会触发blur事件。

`elem.focus()` 和 `elem.blur()` 方法可以设置和移除元素上的焦点。

>**JavaScript 导致的焦点丢失**
>
>其中之一就是用户点击了其它位置。当然 JavaScript 自身也可能导致焦点丢失，例如：
>
>- 一个 `alert` 会将焦点移至自身，因此会导致元素失去焦点（触发 `blur` 事件），而当 `alert` 对话框被取消时，焦点又会重新回到原元素上（触发 `focus` 事件）。
>- 如果一个元素被从 DOM 中移除，那么也会导致焦点丢失。如果稍后它被重新插入到 DOM，焦点也不会回到它身上。



默认情况下一些元素是不支持聚焦的，div span table 等，Html特性 tabindex可以改变这种情况，

任何具有 `tabindex` 特性的元素，都会变成可聚焦的。该特性的 `value` 是当使用 Tab（或类似的东西）在元素之间进行切换时，元素的顺序号。

具有tabindex元素得切换顺序：根据tabinde的value的从小到大，然后再试不具有tabindex的元素，

tabindex的值具有两个特殊属性：

- `tabindex="0"` 会使该元素被与那些不具有 `tabindex` 的元素放在一起。也就是说，当我们切换元素时，具有 `tabindex="0"` 的元素将排在那些具有 `tabindex ≥ 1` 的元素的后面。

  通常，它用于使元素具有焦点，但是保留默认的切换顺序。使元素成为与 `<input>` 一样的表单的一部分。

- `tabindex="-1"` 只允许以编程的方式聚焦于元素。Tab 键会忽略这样的元素，但是 `elem.focus()` 有效。

change 事件

当元素更改完成时 会触发 change事件，比如对于文本框失去焦点时会触发change事件，对于其它元素：`select`，`input type=checkbox/radio`，会在选项更改后立即触发 `change` 事件。

input 事件

当用户对输入值进行修改就会出发 input事件，如果我们想要处理对 <input> 的每次更改，那么此事件是最佳选择。

另一方面，input 事件不会在那些不涉及值更改的键盘输入或其他行为上触发，例如在输入时按方向键 ⇦ ⇨。

cut，copy，paste 事件
这些事件发生于剪切/拷贝/粘贴一个值的时候。我们也可以使用 event.preventDefault() 来中止行为，然后什么都不会被复制/粘贴。



submit 事件

提交表单时，会触发 submit 事件，它通常用于在将表单发送到服务器之前对表单进行校验，或者中止提交，并使用 JavaScript 来处理表单。

提交表单有两种提交方式：

1. 第一种 —— 点击 `<input type="submit">` 或 `<input type="image">`。
2. 第二种 —— 在 `input` 字段中按下 Enter 键。

这两个行为都会触发表单的 `submit` 事件。处理程序可以检查数据，如果有错误，就显示出来，并调用 `event.preventDefault()`，这样表单就不会被发送到服务器了。

>**`submit` 和 `click` 的关系**
>
>在输入框中使用 Enter 发送表单时，会在 `<input type="submit">` 上触发一次 `click` 事件。
>
>这很有趣，因为实际上根本没有点击。



submit 方法

如果要手动将表单提交到服务器，需要手动调用forms.submit()，这样就不会产生submit事件，



总结

在元素获得/失去焦点时会触发 `focus` 和 `blur` 事件。

它们的特点是：

- 它们不会冒泡。但是可以改为在捕获阶段触发，或者使用 `focusin/focusout`。
- 大多数元素默认不支持聚焦。使用 `tabindex` 可以使任何元素变成可聚焦的。

可以通过 `document.activeElement` 来获取当前所聚焦的元素。

| 事件             | 描述                 | 特点                                                         |
| :--------------- | :------------------- | :----------------------------------------------------------- |
| `change`         | 值被改变。           | 对于文本输入，当失去焦点时触发。                             |
| `input`          | 文本输入的每次更改。 | 立即触发，与 `change` 不同。                                 |
| `cut/copy/paste` | 剪贴/拷贝/粘贴行为。 | 行为可以被阻止。`event.clipboardData` 属性可以用于访问剪贴板。除了火狐（Firefox）之外的浏览器都支持 `navigator.clipboard`。 |











