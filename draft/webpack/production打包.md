---
title: 2.3.0 production打包
date: 2018-07-15T15:28:58
tags:
  - vue
  - webpack
  - elementUI
  - typescript
categories:
  - 学习vue
---

这几天准备研究一下，webpack怎么找包发布项目。

<!--more-->

## 打包需求

- 包要尽量小；
- 打包速度尽量快；
- 自动打包静态文件；

## 打包测试

新增一个配置文件 `webpack.prod.conf`

``` typescript
import * as Webpack from 'webpack';
import * as merge from 'webpack-merge';

import { config } from './webpack.conf';

export const prodConfig: Webpack.Configuration = merge.smart(config, {
    mode: 'production',
})

export default prodConfig;
```

`package.json` 中添加命令

``` json
    "scripts":{
        "build:prod": "webpack --config build/webpack.prod.conf.ts",
    }
```

打包完成后，生成的 `app.js` 有 `8.47M`，有点大。

## 去掉sourcemap

首先想到的是去掉 `sourceMap`。添加配置

``` typescript
    devtool: false,
```

打包出来的结果

``` bash
app.js          2.2M
```

## 分离css样式

使用 `extract-text-webpack-plugin` 插件

``` bash
npm i -D extract-text-webpack-plugin @types/extract-text-webpack-plugin
```

打包出来的结果

``` bash
app.js          1,298,009
css/style.css   969,886
```

## 尝试优化一下css

``` bash
npm i -D optimize-css-assets-webpack-plugin @types/optimize-css-assets-webpack-plugin
```

webpack的minimizer中添加：

``` typescript
    new OptimizeCSSPlugin({
        cssProcessorOptions: { map: { inline: false }, discardComments: { removeAll: true } },
    }),
```

打包出来：

``` bash
app.js          1,298,009
css/style.css   184,234
```

## 处理app.js

现在打包出来的js是一个大文件，先按模块折开

### 折分app.js

想法是将所有第三方库合并成一个 `verdor.js`，因为项目现在不太大，可以这样做。

添加`splitChunks`配置

``` typescript
optimization:{
    //添加splitChunks配置
    splitChunks: {
        // chunks: "initial"，"async"和"all"分别是：初始块，按需块或所有块；
        chunks: 'all',
        // （默认值：30000）块的最小大小
        minSize: 30000,
        // （默认值：1）分割前共享模块的最小块数
        minChunks: 1,
        // （缺省值5）按需加载时的最大并行请求数
        maxAsyncRequests: 8,
        // cacheGroups is an object where keys are the cache group names.
        name: true,
        cacheGroups: {
            vendors: {
                test: /[\\/]node_modules[\\/]/,
                name: 'verdors',
            },
        },
    },
}
```

打包出来：

``` bash
app.js          7,608
verdor.js       729,515
css/style.css   184,234
```
