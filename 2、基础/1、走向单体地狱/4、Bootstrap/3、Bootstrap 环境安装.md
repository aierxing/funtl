# [#](https://funtl.com/zh/bootstrap/Bootstrap-环境安装.html#bootstrap-环境安装)Bootstrap 环境安装

## [#](https://funtl.com/zh/bootstrap/Bootstrap-环境安装.html#本节视频)本节视频

- [【视频】使用 Bootstrap-Bootstrap 环境安装](https://www.bilibili.com/video/av24482476/)

## [#](https://funtl.com/zh/bootstrap/Bootstrap-环境安装.html#概述)概述

Bootstrap 安装是非常容易的。本章将讲解如何下载并安装 Bootstrap，讨论 Bootstrap 文件结构，并通过一个实例演示它的用法。

## [#](https://funtl.com/zh/bootstrap/Bootstrap-环境安装.html#下载-bootstrap)下载 Bootstrap

您可以从 http://getbootstrap.com/ 上下载 Bootstrap 的最新版本。

## [#](https://funtl.com/zh/bootstrap/Bootstrap-环境安装.html#文件结构)文件结构

### [#](https://funtl.com/zh/bootstrap/Bootstrap-环境安装.html#预编译的-bootstrap)预编译的 Bootstrap

当您下载了 Bootstrap 的已编译的版本，解压缩 ZIP 文件，您将看到下面的文件/目录结构：

![img](https://funtl.com/assets/Lusifer1526581943.png)

如上图所示，可以看到已编译的 CSS 和 JS（bootstrap.），以及已编译压缩的 CSS 和 JS（bootstrap.min.*）。同时也包含了 Glyphicons 的字体，这是一个可选的 Bootstrap 主题。

## [#](https://funtl.com/zh/bootstrap/Bootstrap-环境安装.html#html-模板)HTML 模板

一个使用了 Bootstrap 的基本的 HTML 模板如下所示：

```html
<!DOCTYPE html>
<html>
   <head>
      <title>Bootstrap 模板</title>
      <meta name="viewport" content="width=device-width, initial-scale=1.0">
      <!-- 引入 Bootstrap -->
      <link href="https://maxcdn.bootstrapcdn.com/bootstrap/css/bootstrap.min.css" rel="stylesheet">
 
      <!-- HTML5 Shiv 和 Respond.js 用于让 IE8 支持 HTML5元素和媒体查询 -->
      <!-- 注意： 如果通过 file://  引入 Respond.js 文件，则该文件无法起效果 -->
      <!--[if lt IE 9]>
         <script src="https://oss.maxcdn.com/libs/html5shiv/3.7.0/html5shiv.js"></script>
         <script src="https://oss.maxcdn.com/libs/respond.js/1.3.0/respond.min.js"></script>
      <![endif]-->
   </head>
   <body>
      <h1>Hello, world!</h1>
 
      <!-- jQuery (Bootstrap 的 JavaScript 插件需要引入 jQuery) -->
      <script src="https://code.jquery.com/jquery.js"></script>
      <!-- 包括所有已编译的插件 -->
      <script src="js/bootstrap.min.js"></script>
   </body>
</html>
```

在这里，您可以看到包含了 jquery.js、bootstrap.min.js 和 bootstrap.min.css 文件，用于让一个常规的 HTML 文件变为使用了 Bootstrap 的模板。

## [#](https://funtl.com/zh/bootstrap/Bootstrap-环境安装.html#实例)实例

现在让我们尝试使用 Bootstrap 输出 "Hello, world!"：

```text
<h1>Hello, world!</h1>
```

上次更新: 2019-4-19 19:23:15