---
title: 前端样式的BEM架构基础实践
author: chaizz
date: 2023-9-16
tags: CSS
categories:
    - CSS
photos: ["https://tc.chaizz.com/760e75941d1511edb5630242ac190002.png"]
cover: "https://tc.chaizz.com/760e75941d1511edb5630242ac190002.png"
---



> element-plus 的架构设计目的为解决当涉及到更大、更复杂的项目时，如何组织代码。
>
> 他的一套方法论是使用一套固定的结构和命名约定。

BEM详细介绍地址：[Link](https://getbem.com/)

其中element-plus 就是采用了bem架构设计的一套样式规则。今天我们使用bem模式来实现一个常见的自适应容器布局。

![image-20230916213942355](https://origin.chaizz.com/tc/image-20230916213942355.png)

## 1 了解BEM的前置知识

### 嵌套样式

```scss
.a {
    .b {
        .c {
            display: flex;
        }
    }
}
```

### $value 定义变量
```scss
$color: blue;
$width: 200px;

.el {
    color: $color;
    width: $width;
}
```



### @at-root : 在嵌套的样式中， 跳出父级节点的类名即不在子类名之前再追加父级类名

```scss
// 正常的样式
// 编译前
.parent {
    display: flex;
    .child {
        display: flex;
    }
}

// 编译后
.parent {
    display: flex;
}

.parent .child {
    display: flex;

// 使用@at-root 后的正常的样式
// 编译前
.parent {
    display: flex;
        @at-root .child {
            display: flex;
    }
}
// 编译后
.parent {
    display: flex;
}
.child {
    display: flex;
}
```



### @mixin：混入, 类似于批量处理,把比较通用的抽离出来，方便统一引用。

```scss
@mixin large-text {
    font: {
        family: Arial;
        size: 20px;
        weight: bold;
    }
}

// 使用方式
.el {
    @include large-text;
    padding: 4px;
}

// 编译后样式即为：
.el {
    font-family: Arial;
    font-size: 20px;
    font-weight: bold;
    padding: 4px;
}

// 混入也可以设置参数

@mixin sexy-boder($color, $width) {
    border: {
        color: $color;
        width: $width;
    }
}

.el {
    @include sexy-boder(blue, 100px);
}
```



### 插值表达式， 在选择器或者属性名称中使用变量

```scss
$name: foo;
$attr: border;

p.#{$name} {
	#{$attr}-color: blue;
}
```



### &：代表当前的父选择器
```scss
.foo.bar .baz.bang,
.bip.qux {
        $selector: &;
}
// & 此时的父选择器为 .foo.bar .baz.bang 和 .bip.qu
```



## 2 在Vue中使用

### 定义bem结构和命名规则

在项目根目录下新建：/src/styles/bem.scss

```scss
// 定义一个变量， 作为开头, 可以任意定义字符，此处以cz为例。 !default： 代表这个变量没有赋值过别的值。
$namespace: "cz" !default;

// Block
$block-sel: "-" !default;

// Element
$elem-sel: "__" !default;

// Modifier
$mod-sel: "--" !default;

//  开始构建block规则结构，例如： <div class="cz-block"></div>
@mixin b($block) {
	// 拼接规则
	$B: #{$namespace + $block-sel + $block};
	// 使用插值表达式
	.#{$B} {
		// @content 代表样式的具体内容，类似占位符的作用
		@content;
	}
}

// 开始构建element规则结构，例如： <div class="cz-block__button"></div>
@mixin e($el) {
	// 拼接规则， 直接读取父级选择器
	$selector: &;
	// 不再需要父级的选择器
	@at-root {
		#{$selector + $elem-sel + $el} {
			@content;
		}
	}
}

// 开始构建Modifier规则结构，例如： <div class="cz-block--success"></div>
@mixin m($m) {
	// 拼接规则， 直接读取父级选择器
	$selector: &;
	// 不再需要父级的选择器
	@at-root {
		#{$selector + $mod-sel + $m} {
			@content;
		}
	}
}

// 定义一个通用规则
@mixin bfc {
	height: 100%;
	overflow: hidden;
}

```



### 在vite.config.ts定义全局css预处理

```tsx
import { defineConfig } from "vite";
import vue from "@vitejs/plugin-vue";

// https://vitejs.dev/config/
export default defineConfig({
	plugins: [vue()],
	css: {
		preprocessorOptions: {
			scss: {
				additionalData: `@import "./src/styles/bem.scss";`,
			},
		},
	},
});
```



## 3 使用BEM模式实现一个容器样式

项目目录

![image-20230916230612119](https://origin.chaizz.com/tc/image-20230916230612119.png)





src/styles/reset.scss

```scss
*,
*:after,
*:before {
	box-sizing: border-box;

	outline: none;
}

html,
body,
div,
span,
applet,
object,
iframe,
h1,
h2,
h3,
h4,
h5,
h6,
p,
blockquote,
pre,
a,
abbr,
acronym,
address,
big,
cite,
code,
del,
dfn,
em,
img,
ins,
kbd,
q,
s,
samp,
small,
strike,
strong,
sub,
sup,
tt,
var,
b,
u,
i,
center,
dl,
dt,
dd,
ol,
ul,
li,
fieldset,
form,
label,
legend,
table,
caption,
tbody,
tfoot,
thead,
tr,
th,
td,
article,
aside,
canvas,
details,
embed,
figure,
figcaption,
footer,
header,
hgroup,
menu,
nav,
output,
ruby,
section,
summary,
time,
mark,
audio,
video {
	font: inherit;
	font-size: 100%;

	margin: 0;
	padding: 0;

	vertical-align: baseline;

	border: 0;
}

article,
aside,
details,
figcaption,
figure,
footer,
header,
hgroup,
menu,
nav,
section {
	display: block;
}

body {
	line-height: 1;
}

ol,
ul {
	list-style: none;
}

blockquote,
q {
	quotes: none;

	&:before,
	&:after {
		content: "";
		content: none;
	}
}

sub,
sup {
	font-size: 75%;
	line-height: 0;

	position: relative;

	vertical-align: baseline;
}

sup {
	top: -0.5em;
}

sub {
	bottom: -0.25em;
}

table {
	border-spacing: 0;
	border-collapse: collapse;
}

input,
textarea,
button {
	font-family: inhert;
	font-size: inherit;

	color: inherit;
}

select {
	text-indent: 0.01px;
	text-overflow: "";

	border: 0;
	border-radius: 0;

	-webkit-appearance: none;
	-moz-appearance: none;
}

select::-ms-expand {
	display: none;
}

code,
pre {
	font-family: monospace, monospace;
	font-size: 1em;
}

html,
body {
	height: 100%;
	overflow: hidden;
}
```

/src/styles/index.scss

```scss
// 引入清除默认的样式
@import './reset.scss'
```

/src/styles/bem.scss

同第二步的scss

```ts
import { createApp } from "vue";
import "./styles/index.scss";
import App from "./App.vue";

createApp(App).mount("#app");

```



/src/main.ts

```vue
<script setup lang="ts">
import Layout from "./Layout/index.vue";

</script>

<template>
    <Layout></Layout>
</template>

<style lang="scss">
#app {
    @include bfc;
}
</style>
```

/src/App.vue

```vue
<script setup lang="ts">
import Layout from "./Layout/index.vue";

</script>

<template>
    <Layout></Layout>
</template>

<style lang="scss">
#app {
    @include bfc;
}
</style>
```



/src/Layout/index.vue

```vue
<script setup lang=ts>
import Header from "./Header/index.vue";
import Content from "./Content/index.vue";
import Menu from "./Menu/index.vue";
</script>

<template>
    <div class="cz-box">
        <Menu></Menu>

        <div class="cz-box__right">
            <Header></Header>
            <Content></Content>
        </div>
    </div>
</template>

<style scoped  lang='scss'>
@include b(box) {
    @include bfc;
    display: flex;

    @include e(right) {
        display: flex;
        flex-direction: column;
        flex: 1;
    }
}
</style>
```

/src/Layout/Menu/index.vue

```vue
<script setup lang=ts></script>

<template>
    <div class="cz-menu">
        Menu
    </div>
</template>

<style scoped  lang='scss'>
@include b(menu) {
    min-width: 200px;
    border-right: 1px solid rgb(57, 22, 184);
    height: 100%;
}
</style>
```

/src/Layout/Header/index.vue

```vue
<script setup lang=ts></script>

<template>
    <div class="cz-header">
        Header
    </div>
</template>

<style scoped  lang='scss'>
@include b(header) {
    height: 100px;
    border-bottom: 1px solid #ccc;
}
</style>
```

/src/Layout/Content/index.vue

```vue
<script setup lang=ts></script>

<template>
    <div class="cz-content">
        <div class="cz-content__items" v-for="(item, index) in 100"> {{ item }} - {{ index }}</div>
    </div>
</template>

<style scoped  lang='scss'>
@include b(content) {
    flex: 1;
    overflow: auto;

    @include e(items) {
        padding: 10px;
        margin: 10px;
        border: 1px solid #ccc;
        border-radius: 4px;
    }
}
</style>
```

## 4 效果

<video src="https://origin.chaizz.com/19_37.webm"></video>
