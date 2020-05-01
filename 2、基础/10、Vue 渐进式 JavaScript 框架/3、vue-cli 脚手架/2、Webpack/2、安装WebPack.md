# [#](https://funtl.com/zh/vue-cli/安装-WebPack.html#安装-webpack)安装 WebPack

## [#](https://funtl.com/zh/vue-cli/安装-WebPack.html#本节视频)本节视频

- [【视频】Vue 渐进式 JavaScript 框架-Webpack-安装和使用](https://www.bilibili.com/video/av43994018/)

## [#](https://funtl.com/zh/vue-cli/安装-WebPack.html#概述)概述

WebPack 是一款模块加载器兼打包工具，它能把各种资源，如 JS、JSX、ES6、SASS、LESS、图片等都作为模块来处理和使用。

## [#](https://funtl.com/zh/vue-cli/安装-WebPack.html#安装)安装

```bash
npm install webpack -g
npm install webpack-cli -g
```

1
2

## [#](https://funtl.com/zh/vue-cli/安装-WebPack.html#配置)配置

创建 `webpack.config.js` 配置文件

- entry：入口文件，指定 WebPack 用哪个文件作为项目的入口
- output：输出，指定 WebPack 把处理完成的文件放置到指定路径
- module：模块，用于处理各种类型的文件
- plugins：插件，如：热更新、代码重用等
- resolve：设置路径指向
- watch：监听，用于设置文件改动后直接打包

```javascript
module.exports = {
    entry: "",
    output: {
        path: "",
        filename: ""
    },
    module: {
        loaders: [
            {test: /\.js$/, loader: ""}
        ]
    },
    plugins: {},
    resolve: {},
    watch: true
}
```

1
2
3
4
5
6
7
8
9
10
11
12
13
14
15

## [#](https://funtl.com/zh/vue-cli/安装-WebPack.html#执行)执行

直接运行 `webpack` 命令打包

上次更新: 2019-2-18 23:00:40

← [Webpack 简介](https://funtl.com/zh/vue-cli/WebPack-简介.html)[使用 WebPack ](https://funtl.com/zh/vue-cli/使用-WebPack.html)→