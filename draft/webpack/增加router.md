---
title: 2.2.0 使用vue的router跳转
date: 2018-06-20T15:28:58
tags:
  - vue
  - webpack
  - elementUI
  - typescript
categories:
  - 学习vue
---

# 增加router模块

在 `src/router` 目录下新建 `index.ts`, 用来定义整个页面的路由。

``` typescript
import Vue from 'vue';
import VueRouter, { RouteConfig } from 'vue-router';

Vue.use(VueRouter);

import Layout from '@/views/layout/index.vue';

export const staticRouter: RouteConfig[] = [
    {
        path: '/', component: Layout, redirect: 'dashboard'
    }
]
```

<!--more-->

然后在 `src/index.ts` 里面需要把 `router` 引入。

``` typescript
import router from '@/router';

const v = new Vue({
    el: '#app',
    router,
    ......
});
```

# 新建页面 Layout

在 `src/views/layout` 目录下新建 `index.vue`, 用来定义整个页面的模板。

先做个简单的页面。

``` html
<template>
    <div class="app-wrapper">
        <div class="sidebar">
            <ul>
                <li>
                    <a href="/">Index</a>
                </li>
                <li>
                    <a href="/pages">Pages</a>
                </li>
                <li>
                    <a href="/about">About</a>
                </li>
            </ul>
        </div>
        <router-view></router-view>
    </div>
</template>

<script lang="ts">
import { Vue, Prop, Component } from "vue-property-decorator";
export default class Layout extends Vue {}
</script>

```

运行一下看结果。可以正常导航了。