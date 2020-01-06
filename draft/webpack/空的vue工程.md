---
title: 2.1.0 Vue单页工程
date: 2018-06-10T15:28:58
tags:
  - vue
  - webpack
  - elementUI
  - typescript
categories:
  - 学习vue
---

# 安装依赖

我们需要 `vue-property-decorator` 来引入 `Prop` 和 `Component`。

``` bash
npm i -s vue-property-decorator
```

# `Vue`单页工程

在 `src` 目录下新建 `app.vue`文件

``` html
<template>
    <div id="app">
        <h1>Hello world</h1>
    </div>
</template>

<script lang="ts">
import { Vue, Prop, Component } from "vue-property-decorator";

@Component
export default class AppComponent extends Vue {}
</script>
```

<!--more-->

修改 `index.ts` 中的内容

``` typescript
import AppComponent from '@/app.vue';

const v = new Vue({
    ...
    template: `<app-component></app-component>`,
    components: {
        AppComponent,
    },
    ...
});
```

这里发现在，提示：“`[ts] 找不到模块“@/app.vue”。`” 这是因为我们没有告诉 `typescript` 如何处理 `.vue` 文件。

这里我们新建一个 `vue-shims.d.ts`

``` typescript
declare module "*.vue" {
    import Vue from "vue";
    export default Vue;
}
```

这样错误提示就解决了。

`npm start` 预览一下。`Hello World`。
