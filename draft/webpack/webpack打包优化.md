---
title: 2.4.0 webpack打包体积和速度优化
date: 2018-07-20T10:10:58
keywords: "vue, webpack, elementUI, typescript"
tags:
  - webpack
categories:
  - 学习vue
---

# typescript的tree shaking

使用webpack打包typescript时，发现没有tree shaking。

分析对比，发现是因为webpack支持的tsconfig配置文件中有说到要用：`"module": "CommonJS",`，

但是在webpack中关于tree shaking的说明里面提到

   Tree shaking is a term commonly used in the JavaScript context for dead-code elimination. It relies on the static structure of ES2015 module syntax, i.e. import and export. The name and concept have been popularized by the ES2015 module bundler rollup.

tree shaking要用es2015的才行。所以要分开两个配置文件。`tsconfig.json`和`tsconfig.webpack.json`，其中`tsconfig.webpack.json`用来打包用。

`tsconfig.json`

``` json
"module": "CommonJS",
"target": "es2015",
```

`tsconfig.webpack.json`

``` json
"module": "es2015",
"target": "es2015",
```

<!--more-->

修改`webpack.prod.conf.ts`

``` typescript
{
                test: /\.vue$/,
                loader: 'vue-loader',
                options: {
                    loaders: {
                        ts: 'cache-loader!babel-loader!ts-loader?configFile=tsconfig.webpack.json',
                        // Since sass-loader (weirdly) has SCSS as its default parse mode, we map
                        // the "scss" and "sass" values for the lang attribute to the right configs here.
                        // other preprocessors should work out of the box, no loader config like this necessary.
                        scss: 'vue-style-loader!css-loader!sass-loader',
                        sass: 'vue-style-loader!css-loader!sass-loader?indentedSyntax',
                    },
                    // other vue-loader options go here
                },
                include: /src/,
                exclude: /node_modules\/(?!(autotrack|dom-utils))|vendor\.dll\.js/,
            },
            {
                test: /\.tsx?$/,
                loader: 'ts-loader',
                exclude: /node_modules/,
                include: /src/,
                options: {
                    configFile: "tsconfig.webpack.json",
                    appendTsSuffixTo: [/\.vue$/],
                },
            },
```

打包测试一下，`app.js`从`12k`减小到`7k`，变化明显。

# 优化打包速度

使用dll插件可以优化打包速度，首先新建一个`dll`专用的打包配置

``` typescript
import * as OptimizeCSSAssetsPlugin from 'optimize-css-assets-webpack-plugin';
import * as path from 'path';
import * as UglifyJsPlugin from 'uglifyjs-webpack-plugin';
import * as webpack from 'webpack';

module.exports = {
    mode: 'development',
    entry: {
        vendor: [
            '@fortawesome/fontawesome-svg-core',
            '@fortawesome/free-solid-svg-icons',
            '@fortawesome/vue-fontawesome',
            'vue',
            'element-ui',
            'vue-router',
        ],
    },
    output: {
        path: path.resolve(__dirname, '../static/js'),
        filename: '[name].dll.js',
        library: '[name]_library',
    },
    module: {
        rules: [
            {
                test: /\.vue$/,
                loader: 'vue-loader',
            },
            {
                test: /\.js$/,
                loader: 'babel-loader',
                exclude: /node_modules/,
            },
            {
                test: /\.tsx?$/,
                loader: 'ts-loader',
                exclude: /node_modules/,
            },
        ],
    },
    optimization: {
        minimizer: [
            new UglifyJsPlugin({
                cache: true,
                parallel: true,
                sourceMap: false, // set to true if you want JS source maps
            }),
            new OptimizeCSSAssetsPlugin({}),
        ],
    },
    plugins: [
        new webpack.optimize.ModuleConcatenationPlugin(),
        new webpack.DllPlugin({
            path: path.join(__dirname, '../static/js', '[name]-manifest.json'),
            name: '[name]_library',
        }),
    ],
};
```

这个配置用来生成`verdor.dll.js`文件，然后修改`webpack.prod.conf.ts`配置，引用`verdor.dll.js`。

plugins里面增加：

``` typescript
new Webpack.DllReferencePlugin({
    context: __dirname,
    manifest: require('../static/js/vendor-manifest.json'),
}),
```

先运行 `webpack --config build/webpack.dll.conf.ts` 生成dll，然后运行 `cross-env NODE_ENV=production webpack --config build/webpack.prod.conf.ts`。

测试打包速度比之前快了几秒钟。
