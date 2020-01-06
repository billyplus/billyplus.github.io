---
title: 2.0.0 webpack打包
date: 2018-06-03T15:28:58
keywords: "vue, webpack, elementUI, typescript"
tags:
  - vue
  - webpack
categories:
  - 学习vue
---

# 安装其它包

## 打包插件

``` shell
npm i -D vue-loader ts-loader cache-loader style-loader css-loader url-loader file-loader html-webpack-plugin vue-template-compiler
```

## 为了使用 .ts 格式的 webpack 配置

``` shell
npm i -D  ts-node @types/html-webpack-plugin @types/node @types/webpack @types/webpack-merge
```

## babel相关的包

``` shell
npm i -D @babel/core @babel/preset-typescript babel-loader
```

<!--more-->

# 显示 webpack 打包进度

## 安装包

``` shell
npm i -D chalk progress-bar-webpack-plugin
```

这里要注意，会提示：“**无法找到模块“progress-bar-webpack-plugin”的声明文件。**”

因为用的是typescript，所以需要有声明文件，也就是常见的`.d.ts`文件，网上没有现成的，我们需要自己手动写一个。把声明文件放到前面我们在`tsconfig.json`里面定义的`types`目录里面。

在`types`目录新建文件夹`progress-bar-webpack-plugin`，然后在里面新建文件`index.d.ts`。

``` typescript
import { Plugin } from 'webpack';

export = ProgressBarPlugin;
declare namespace ProgressBarPlugin {
    interface ProgressBarOptions {
        format: string;

    }
}
declare class ProgressBarPlugin extends Plugin {
    constructor(options?: ProgressBarPlugin.ProgressBarOptions);
}
```

有了这个定义就不会报错了。

# 准备webpack配置

`build` 目录下新建文件 `webpack.conf.ts`，具体内容太长就不复制了，直接在项目中看吧。

# 脚本

在`package.json`里面增加脚本：

``` json
  "scripts": {
    "start": "webpack-dev-server --config build/webpack.conf.ts",
    "build:dev": "webpack --config build/webpack.conf.ts"
  },
```

# 测试

完成以上工作，开发环境就算准备好了。

在工程根目录新建一个`index.html`模板。

``` html
<!doctype html>
<html>

<head>
    <meta charset="utf-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge,chrome=1">
    <meta name="renderer" content="webkit">
    <meta name="viewport" content="width=device-width, initial-scale=1, maximum-scale=1, user-scalable=no">
    <title>
        <%= htmlWebpackPlugin.options.title %>
    </title>
</head>

<body>
    <div id="app"></div>
</body>

</html>
```

在`src`目录新建`index.ts`：

``` typescript
import Vue from 'vue';

import ElementUI from 'element-ui';
import 'element-ui/lib/theme-chalk/index.css';

Vue.use(ElementUI);

const v = new Vue({
    el: '#app',
    template: `<p>hello world</p>`,
    components: {
    },
});
```

然后运行 `npm run start`

打开 `http://localhost:9000` 看一下。
