---
title: 解决webpack无法将css打包成单个文件的问题
date: 2018-08-10T16:24:58
keywords: "sideEffects, webpack, 打包css, typescript"
tags:
  - webpack
categories:
  - 使用经验
---

# 遇到的问题

在打包一个旧项目的时候，遇到所有的`css`都丢失了的情况。检查文件发现没有导出`style.css`这个文件。

按照打包配置，应该会把项目中所有的`css`整合成一个文件导出的，但是打包过程正常，`style.css`却没有导出。

找了半天都没有找出问题，试了多种打包方案，把`css`拆开的、合在一起的、把`extract-text-webpack-plugin`插件换成`mini-css-extract-plugin`插件……

整个打包过程没有任何错误提示，导致查错没有方向。

# 是sideEffects的锅

最后在 [stackoverflow](https://stackoverflow.com/questions/50416293/webpack-minicssextractplugin-doesnt-extract-file) 这个问题上受到了启发。

   Note that any imported file is subject to tree shaking. This means if you use something like css-loader in your project and import a CSS file, it needs to be added to the side effect list so it will not be unintentionally dropped in production mode.

`Webpack`文档里面这样说：

    A "side effect" is defined as code that performs a special behavior when imported, other than exposing one or more exports. An example of this are polyfills, which affect the global scope and usually do not provide an export.

    "side effect"就是一段导入后执行特殊功能的代码，比如：`polyfills`的是直接作用在全局的，它并没有导出。

<!--more-->

想起前几天尝试了一下`sideEffects`的作用，于是赶紧把它改回来。在项目的`package.json`里面找到`sideEffects: false`，直接删除掉。

当然，也可以加入例外

``` json
"sideEffects": [
    '.scss'
 ]
```

再打包，`style.css`回来了。
