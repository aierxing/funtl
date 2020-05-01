# [#](https://funtl.com/zh/vue-cli/使用-WebPack.html#使用-webpack)使用 WebPack

## [#](https://funtl.com/zh/vue-cli/使用-WebPack.html#概述)概述

使用 WebPack 打包项目非常简单，主要步骤如下：

- 创建项目
- 创建一个名为 `modules` 的目录，用于放置 JS 模块等资源文件
- 创建模块文件，如 `hello.js`，用于编写 JS 模块相关代码
- 创建一个名为 `main.js` 的入口文件，用于打包时设置 `entry` 属性
- 创建 `webpack.config.js` 配置文件，使用 `webpack` 命令打包
- 创建 HTML 页面，如 `index.html`，导入 WebPack 打包后的 JS 文件
- 运行 HTML 看效果

## [#](https://funtl.com/zh/vue-cli/使用-WebPack.html#目录结构)目录结构

![img](https://funtl.com/assets1/Lusifer_20190212015555.png)

## [#](https://funtl.com/zh/vue-cli/使用-WebPack.html#模块代码)模块代码

创建一个名为 `hello.js` 的 JavaScript 模块文件，代码如下：

```javascript
exports.sayHi = function () {
  document.write("<div>Hello WebPack</div>");
};
```

1
2
3

## [#](https://funtl.com/zh/vue-cli/使用-WebPack.html#入口代码)入口代码

创建一个名为 `main.js` 的 JavaScript 入口模块，代码如下：

```javascript
var hello = require("./hello");
hello.sayHi();
```

1
2

## [#](https://funtl.com/zh/vue-cli/使用-WebPack.html#配置文件)配置文件

创建名为 `webpack.config.js` 的配置文件，代码如下：

```javascript
module.exports = {
    entry: "./modules/main.js",
    output: {
        filename: "./js/bundle.js"
    }
};
```

1
2
3
4
5
6

## [#](https://funtl.com/zh/vue-cli/使用-WebPack.html#html)HTML

创建一个名为 `index.html`，代码如下：

```html
<!doctype html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport"
          content="width=device-width, user-scalable=no, initial-scale=1.0, maximum-scale=1.0, minimum-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Document</title>
</head>
<body>
<script src="dist/js/bundle.js"></script>
</body>
</html>
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

## [#](https://funtl.com/zh/vue-cli/使用-WebPack.html#打包)打包

```bash
# 用于监听变化
webpack --watch
```

1
2

## [#](https://funtl.com/zh/vue-cli/使用-WebPack.html#运行)运行

运行 HTML 文件，你会在浏览器看到：

```html
Hello WebPack
```

1

上次更新: 2019-2-17 1:56:17

← [安装 WebPack](https://funtl.com/zh/vue-cli/安装-WebPack.html)