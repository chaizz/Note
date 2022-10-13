---
title: js笔记之Js事件
author: chaizz
date: 2022-10-13 17:40:18
tags: Javascript
photo: ["https://tc.chaizz.com/ec55444c4a1211edac740242ac190002.png"]
---

​          

<!--more-->

# 事件冒泡及捕获



js的事件冒泡和捕获是两种机制，主要描述一个元素上有两个相同类型的事件处理器被激活会发生什么。

例如该例子：[源码](https://github.com/mdn/learning-area/blob/main/javascript/building-blocks/events/show-video-box.html)

他显示和隐藏一个包含 <video> 元素的 <div> 元素。

```html
<button>Display video</button>

<div class="hidden">
    <video>
        <source src="https://raw.githubusercontent.com/mdn/learning-area/master/javascript/building-blocks/events/rabbit320.mp4"
                type="video/mp4">
        <source src="https://raw.githubusercontent.com/mdn/learning-area/master/javascript/building-blocks/events/rabbit320.webm"
                type="video/webm">
        <p>Your browser doesn't support HTML video. Here is a <a href="rabbit320.mp4">link to the video</a> instead.
        </p>
    </video>
</div>
```



当`button`元素按钮被单击时，他将显示视频，其实改变`div`的`class`属性值从`hidden`变`showing`。

```js
const btn = document.querySelector('button');
const videoBox = document.querySelector('div');

function displayVideo() {
    if (videoBox.getAttribute('class') === 'hidden') {
        videoBox.setAttribute('class', 'showing');
    }
}

btn.addEventListener('click', displayVideo);
```

当我们再添加几个`click`事件处理器，第一个添加在`<div>`元素上，第二个添加在`<video>`元素上。这个想法是当视频 (`<video>`）外 `<div>`元素内这块区域被单击时，这个视频盒子应该再次隐藏；当单击视频 (`<video>`）本身，这个视频将开始播放。

```js
videoBox.addEventListener('click', () => videoBox.setAttribute('class', 'hidden'));

const video = document.querySelector('video');

video.addEventListener('click', () => video.play());
```



## 1 问题就出现了

当点击`video`开始播放视频时，`div`被隐藏， 这是因为`video`在`div`之内，所以点击`video`实际上也同时运行了`div`上的事件处理程序。



综合以上示例：

当一个事件发生在具有父元素的元素上（本例子的video元素）时，现代浏览器运行两个不同的阶段-捕获阶段和冒泡阶段。

在捕获阶段：

- 浏览器检查元素的最外层祖先`html`，是否在捕获阶段中注册了一个onclick事件处理程序，如果是，则运行它。
- 然后，他移动到`html`中点击元素的下一个祖先元素，并执行相同的操作，接下来是点击元素再下一个祖先元素，以此类推，直到到达实际点击的元素。

在冒泡阶段恰恰相反：

- 浏览器检查实际点击的元素是否在冒泡阶段中注册了一个`onclick`事件处理程序，如果是，则运行它。
- 然后他移动到下一个直接的祖先元素，并做同样的事情，再到下一个祖先元素，直到他到达`html`元素。

**在现代浏览器中，默认情况下，所有的时间处理程序，都是在冒泡阶段进行注册。** 因此上述例子当我们点击视频时，这个单击事件从`video`元素向外冒泡直到`html`元素，沿着这个事件冒泡线路：

- 首先发现了`video.onclick...`事件处理器并且运行它，因此这个视频`<video>`第一次开始播放。
- 接着还发现了`videoBox.onclick...`事件处理器并且运行它，因此这个视频`<video>`也隐藏起来了。

## 2 解决方法：

标准事件对象具有可用的名为 [`stopPropagation()`](https://developer.mozilla.org/zh-CN/docs/Web/API/Event/stopPropagation)的函数，当在事件对象上调用该函数时，它只会让当前事件处理程序运行，但事件不会在**冒泡**链上进一步扩大，因此将不会有更多事件处理器被运行 (不会向上冒泡)。所以，我们可以通过改变前面代码块中的第二个处理函数来解决当前的问题：

```js
// 将
video.addEventListener('click', () => video.play());
// 改为：
video.onclick = function(e) {
    e.stopPropagation();
    video.play();
};
```



**注：**如上所述，默认情况下，所有事件处理程序都是在冒泡阶段注册的，这在大多数情况下更有意义。如果您真的想在捕获阶段注册一个事件，那么可以通过使用[`addEventListener()`](https://developer.mozilla.org/zh-CN/docs/Web/API/EventTarget/addEventListener)注册您的处理程序，并将可选的第三个属性设置为 true。

## 3 延伸

### 事件委托

正式因为冒泡的机制， 所以我们可以利用该机制，实现下面这样的行为：如果你想要在大量子元素中单击任何一个都可以运行一段代码，您可以将事件监听器设置在其父节点上，并让子节点上发生的事件冒泡到父节点上，而不是每个子节点单独设置事件监听器。这种行为称之为事件委托。这个概念的更多的例子-[How JavaScript Event Delegation Works](https://davidwalsh.name/event-delegate)。



