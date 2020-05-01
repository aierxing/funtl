# [#](https://funtl.com/zh/vue-cli/vue-cli-目录结构.html#vue-cli-目录结构)vue-cli 目录结构

## [#](https://funtl.com/zh/vue-cli/vue-cli-目录结构.html#本节视频)本节视频

- [【视频】Vue 渐进式 JavaScript 框架-Vuecli-目录结构](https://www.bilibili.com/video/av43993862/)

## [#](https://funtl.com/zh/vue-cli/vue-cli-目录结构.html#概述)概述

![img](https://funtl.com/assets/Lusifer_20190101111159.png)

- build 和 config：WebPack 配置文件
- node_modules：用于存放 `npm install` 安装的依赖文件
- **src：** 项目源码目录
- static：静态资源文件
- .babelrc：Babel 配置文件，主要作用是将 ES6 转换为 ES5
- .editorconfig：编辑器配置
- eslintignore：需要忽略的语法检查配置文件
- .gitignore：git 忽略的配置文件
- .postcssrc.js：css 相关配置文件，其中内部的 `module.exports` 是 NodeJS 模块化语法
- index.html：首页，仅作为模板页，实际开发时不使用
- package.json：项目的配置文件
  - name：项目名称
  - version：项目版本
  - description：项目描述
  - author：项目作者
  - scripts：封装常用命令
  - dependencies：生产环境依赖
  - devDependencies：开发环境依赖

上次更新: 2019-4-19 19:23:15

← [第一个 vue-cli 应用程序](https://funtl.com/zh/vue-cli/)[vue-cli src 目录 ](https://funtl.com/zh/vue-cli/vue-cli-src.html)→