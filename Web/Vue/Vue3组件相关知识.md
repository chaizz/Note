---
title: Vue3组件相关知识
author: chaizz
date: 2023-9-17
tags: Vue3
categories:
    - Vue3
photos: ["https://origin.chaizz.com/tc/Vue.js_Logo_2.svg"]
cover: "https://origin.chaizz.com/tc/Vue.js_Logo_2.svg"
---



# Vue3组件相关知识





当我们去开发一个项目时，出现频率比较高的一个组件我们可以把他抽离为一个==全局组件==，直接在其他的组件上使用。当我们在一个页面上模块非常多，我们可以把每个模块当做一个==局部组件==，在一个页面内引入使用。==递归组件==就是我们需要重复的在一个组件内复用自己，比如像是菜单或者是树形结构，就是通过递归得到的结构。

同时我们也可以在某个组件中动态的切换其他的组件，来达到v-if的效果。

## 局部组件

我们定义的每一个Vue文件就是一个组件， 在使用局部组件时直接引入就可以直接使用。



## 全局组件

注册全局组件的方式

```tsx
// main.ts

import Example from 'xxx/xxx/index.vue' 

// 第一个参数：组件名,可以任意定义，此处和组件实例同名
// 第二个参数：引入的组件实例
app.component('Example', Example)

app.mount('#app')
```

使用的方式：

直接在其他的任意组件中使用 `<Example></Example>`

多个组件可以参考element-plus的 icon 组件的批量注册方式：

```ts
// main.ts

// 如果您正在使用CDN引入，请删除下面一行。
import * as ElementPlusIconsVue from '@element-plus/icons-vue'

const app = createApp(App)
for (const [key, component] of Object.entries(ElementPlusIconsVue)) {
  app.component(key, component)
}
```



## 递归组件

在使用递归组件时第个一条件就是确定调用组件名称，第一种方式就是直接使用当前的文件名当做组件名。

父组件数据：

```js
import { ref } from 'vue'

const data = ref([
    {
        name: "1",
        check: true,
    },
    {
        name: "2",
        check: false,
        children: [
            {
                name: "2-1",
                check: true,
            }
        ]
    },
    {
        name: "3",
        check: false,
        children: [
            {
                name: "3-1",
                check: true,
                children: [
                    {
                        name: "3-1-1",
                        check: true,
                    },
                    {
                        name: "3-1-2",
                        check: false,
                    }
                ]
            }
        ]
    },
])

```

子组件递归调用：

```vue
<script setup lang=ts>
import { ref } from 'vue';
defineProps(['data'])

</script>

<template>
    <div v-for="item in data">
        <input type="checkbox" v-model="item.check">
        <span>{{ item.name }}</span>
        <Tree v-if="item?.children?.length" :data="item?.children"></Tree>
    </div>
</template>

<style scoped  lang='scss'></style>
```



如果不使用自身的文件名当做递归的组件名称， 还可以在重新开启一个新的script标签，但是不能写 setup。

例如：

```vue
<script lang="ts">
export default {
    name: "componentName"
}
</script>
```

在组件中使用可以直接使用componentName， `<componentName></componentName>`。

弊端就是还需要再重新写一个script的标签， 很繁琐。

另一种方式就是需要安装一个插件：`unplugin-vue-define-options` 然后在需要一通设置。

在使用递归组件时，使用点击事件会有一个事件冒泡的问题，所以如果使用事件的话要添加`@click.stop=eventHandler`。 接受参数可以使用`$event`



## 动态组件

先说一下使用场景， 比如我们在做一些 tab页切换时，可以使用路由或者是v-if或者是**动态组件**。

![](https://origin.chaizz.com/tc/image-20230917204507008.png)

直接上示例代码

```vue
<script setup lang=ts>
import { ref, reactive } from 'vue';
import ACom from './components/A.vue'
import BCom from './components/B.vue'
import CCom from './components/C.vue'

// 待切换的组件数组
const componentsArr = reactive([
    {
        name: "A",
        com: ACom
    },
    {
        name: "B",
        com: BCom
    },
    {
        name: "C",
        com: CCom
    }
])

// 绑定的动态组件
const activeCom = ref(ACom)

// tab的选中样式类
const activate = ref(0)

// 切换事件
const switchHandler = (item, index) => {
    activate.value = index
    activeCom.value = item.com
}
</script>

<template>
    <div class="box">
        <div class="tab">
            <div @click="switchHandler(item, index)" class="tab-item" :class="activate == index ? 'activate' : ''"
                v-for="(item, index) in componentsArr">
                <div>{{ item.name }}</div>
            </div>
        </div>
        <component :is="activeCom"></component>
    </div>
</template>

<style scoped  lang='scss'>
.activate {
    color: white;
    background-color: green;
}

.box {
    display: flex;
    flex-direction: column;
    margin-left: 10px;
    margin-top: 10px;

    .tab {
        display: flex;

        .tab-item {
            cursor: pointer;
            width: 100px;
            height: 50px;
            margin-right: 10px;
            border: 1px solid gray;
            display: flex;
            align-items: center;
            justify-content: center;
        }
    }
}
</style>
```



![](https://origin.chaizz.com/tc/image-20230917213214203.png)



但是会有一个问题，vue会抛出一个警告：

![](https://origin.chaizz.com/tc/image-20230917213640949.png)

大概是因为vue对切换的组件中的对象也做了一个代理，但是我们这个场景没有必要在做一层代理 所以可以使用 `markRaw`, `shallowRef` 来做一个性能优化， 来避免再次代理。

最终的代码为

```vue
<script setup lang=ts>
import { ref, reactive, markRaw, shallowRef } from 'vue';
import ACom from './components/A.vue'
import BCom from './components/B.vue'
import CCom from './components/C.vue'

const componentsArr = reactive([
    {
        name: "A",
        com: markRaw(ACom)
    },
    {
        name: "B",
        com: markRaw(BCom)
    },
    {
        name: "C",
        com: markRaw(CCom)
    }
])

// 绑定的动态组件
const activeCom = shallowRef(ACom)

// tab的选中样式类
const activate = ref(0)

// 切换事件
const switchHandler = (item, index) => {
    activate.value = index
    activeCom.value = item.com
}
</script>

<template>
    <div class="box">
        <div class="tab">
            <div @click="switchHandler(item, index)" class="tab-item" :class="activate == index ? 'activate' : ''"
                v-for="(item, index) in componentsArr">
                <div>{{ item.name }}</div>
            </div>
        </div>
        <component :is="activeCom"></component>
    </div>
</template>

<style scoped  lang='scss'>
.activate {
    color: white;
    background-color: green;
}

.box {
    display: flex;
    flex-direction: column;
    margin-left: 10px;
    margin-top: 10px;

    .tab {
        display: flex;

        .tab-item {
            cursor: pointer;
            width: 100px;
            height: 50px;
            margin-right: 10px;
            border: 1px solid gray;
            display: flex;
            align-items: center;
            justify-content: center;
        }
    }
}
</style>
```

具体的优化方式可以看下这个满神的[这个视屏](https://www.bilibili.com/video/BV1dS4y1y7vd/?p=18&spm_id_from=pageDriver&vd_source=623a2a45ce286fe2d8644ddb7b5db1c6)。





















