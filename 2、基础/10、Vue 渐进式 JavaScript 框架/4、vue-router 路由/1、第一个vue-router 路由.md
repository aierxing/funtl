# 第一个 vue-router 路由

## [#](https://funtl.com/zh/vue-router/#本节视频)本节视频

- [【视频】Vue 渐进式 JavaScript 框架-VueRouter-第一个路由](https://www.bilibili.com/video/av43994080/)

## [#](https://funtl.com/zh/vue-router/#概述)概述

Vue Router 是 Vue.js 官方的路由管理器。它和 Vue.js 的核心深度集成，让构建单页面应用变得易如反掌。包含的功能有：

- 嵌套的路由/视图表
- 模块化的、基于组件的路由配置
- 路由参数、查询、通配符
- 基于 Vue.js 过渡系统的视图过渡效果
- 细粒度的导航控制
- 带有自动激活的 CSS class 的链接
- HTML5 历史模式或 hash 模式，在 IE9 中自动降级
- 自定义的滚动条行为

## [#](https://funtl.com/zh/vue-router/#安装)安装

vue-router 是一个插件包，所以我们还是需要用 npm/cnpm 来进行安装的。打开命令行工具，进入你的项目目录，输入下面命令。

```bash
npm install vue-router --save-dev
```

1

如果在一个模块化工程中使用它，必须要通过 `Vue.use()` 明确地安装路由功能：

```javascript
import Vue from 'vue'
import VueRouter from 'vue-router'

Vue.use(VueRouter);
```

1
2
3
4

## [#](https://funtl.com/zh/vue-router/#使用)使用

以下案例在 vue-cli 项目中使用 vue-router

### [#](https://funtl.com/zh/vue-router/#创建组件页面)创建组件页面

创建一个名为 `src/components` 的目录专门放置我们开发的 Vue 组件，在 `src/components` 目录下创建一个名为 `Content.vue` 的组件，代码如下：

```vue
<template>
    <div>
      我是内容页
    </div>
</template>

<script>
    export default {
        name: "Content"
    }
</script>

<style>
  #app {
    font-family: 'Avenir', Helvetica, Arial, sans-serif;
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

### [#](https://funtl.com/zh/vue-router/#安装路由)安装路由

创建一个名为 `src/router` 的目录专门放置我们的路由配置代码，在 `src/router` 目录下创建一个名为 `index.js` 路由配置文件，代码如下：

```javascript
import Vue from 'vue'
// 导入路由插件
import Router from 'vue-router'
// 导入上面定义的组件
import Content from '@/components/Content'

// 安装路由
Vue.use(Router);

// 配置路由
export default new Router({
  routes: [
    {
      // 路由路径
      path: '/content',
      // 路由名称
      name: 'Content',
      // 跳转到组件
      component: Content
    }
  ]
});
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

### [#](https://funtl.com/zh/vue-router/#配置路由)配置路由

修改 `main.js` 入口文件，增加配置路由的相关代码

```javascript
import Vue from 'vue'
import App from './App'
// 导入上面创建的路由配置目录
import router from './router'

Vue.config.productionTip = false;

new Vue({
  el: '#app',
  // 配置路由
  router,
  components: { App },
  template: '<App/>'
});
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

### [#](https://funtl.com/zh/vue-router/#使用路由)使用路由

修改 `App.vue` 组件，代码如下：

```vue
<template>
  <div id="app">
    <router-link to="/">首页</router-link>
    <router-link to="/content">内容</router-link>
    <router-view></router-view>
  </div>
</template>

<script>
export default {
  name: 'App'
}
</script>

<style>
  #app {
    font-family: 'Avenir', Helvetica, Arial, sans-serif;
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

说明：

- **router-link：** 默认会被渲染成一个 `` 标签，`to` 属性为指定链接
- **router-view：** 用于渲染路由匹配到的组件

### [#](https://funtl.com/zh/vue-router/#效果演示)效果演示

![img](https://funtl.com/assets1/Lusifer_2019021504350001.gif)

上次更新: 2019-2-18 23:03:15

[第一个 Vue 工程项目 ](https://funtl.com/zh/vue-router/第一个-Vue-工程项目.html)→