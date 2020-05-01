# [#](https://funtl.com/zh/vue-cli/vue-cli-src.html#vue-cli-src-目录)vue-cli src 目录

## [#](https://funtl.com/zh/vue-cli/vue-cli-src.html#概述)概述

`src` 目录是项目的源码目录，所有代码都会写在这里

![img](https://funtl.com/assets1/Lusifer_20190106170155.png)

## [#](https://funtl.com/zh/vue-cli/vue-cli-src.html#main-js)main.js

项目的入口文件，我们知道所有的程序都会有一个入口

```javascript
// The Vue build version to load with the `import` command
// (runtime-only or standalone) has been set in webpack.base.conf with an alias.
import Vue from 'vue'
import App from './App'

Vue.config.productionTip = false

/* eslint-disable no-new */
new Vue({
  el: '#app',
  components: { App },
  template: '<App/>'
})
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

- `import Vue from 'vue'`：ES6 写法，会被转换成 `require("vue");` （require 是 NodeJS 提供的模块加载器）

- `import App from './App'`：意思同上，但是指定了查找路径，`./` 为当前目录

- `Vue.config.productionTip = false`：关闭浏览器控制台关于环境的相关提示

- ```
  new Vue({...})
  ```

  ：实例化 Vue

  - `el: '#app'`：查找 index.html 中 id 为 app 的元素
  - `template: ''`：模板，会将 index.html 中 `` 替换为 ``
  - `components: { App }`：引入组件，使用的是 `import App from './App'` 定义的 App 组件

## [#](https://funtl.com/zh/vue-cli/vue-cli-src.html#app-vue)App.vue

组件模板

```vue
<template>
  <div id="app">
    <img src="./assets/logo.png">
    <HelloWorld/>
  </div>
</template>

<script>
import HelloWorld from './components/HelloWorld'

export default {
  name: 'App',
  components: {
    HelloWorld
  }
}
</script>

<style>
#app {
  <!-- 字体 -->
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  <!-- 文字平滑效果 -->
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
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
16
17
18
19
20
21
22
23
24
25
26
27
28
29
30

- `template`：HTML 代码模板，会替换 `` 中的内容

- `import HelloWorld from './components/HelloWorld'`：引入 HelloWorld 组件，用于替换 `template` 中的 ``

- ```
  export default{...}
  ```

  ：导出 NodeJS 对象，作用是可以通过

   

  ```
  import
  ```

   

  关键字导入

  - `name: 'App'`：定义组件的名称
  - `components: { HelloWorld }`：定义子组件

## [#](https://funtl.com/zh/vue-cli/vue-cli-src.html#helloworld-vue)HelloWorld.vue

基本同上，不解释..

关于 `` 的说明：CSS 样式仅在当前组件有效，声明了样式的作用域

上次更新: 2019-1-8 3:00:51

← [vue-cli 目录结构](https://funtl.com/zh/vue-cli/vue-cli-目录结构.html)[附：Mac 系统操作 Vue 的权限问题 ](https://funtl.com/zh/vue-cli/附：Mac-系统操作-Vue-的权限问题.html)→