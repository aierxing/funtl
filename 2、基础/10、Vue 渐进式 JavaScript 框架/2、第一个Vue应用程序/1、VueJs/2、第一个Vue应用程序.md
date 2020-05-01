# [#](https://funtl.com/zh/vue/第一个-Vue-应用程序.html#第一个-vue-应用程序)第一个 Vue 应用程序

## [#](https://funtl.com/zh/vue/第一个-Vue-应用程序.html#本节视频)本节视频

- [【视频】Vue 渐进式 JavaScript 框架-Vue-第一个应用程序](https://www.bilibili.com/video/av43521927/)

## [#](https://funtl.com/zh/vue/第一个-Vue-应用程序.html#兼容性)兼容性

Vue **不支持** IE8 及以下版本，因为 Vue 使用了 IE8 无法模拟的 ECMAScript 5 特性。但它支持所有**兼容 ECMAScript 5 的浏览器**。

## [#](https://funtl.com/zh/vue/第一个-Vue-应用程序.html#下载地址)下载地址

- 开发版本
  - 包含完整的警告和调试模式：https://vuejs.org/js/vue.js
  - 删除了警告，30.96KB min + gzip：https://vuejs.org/js/vue.min.js
- CDN
  - ``
  - ``

## [#](https://funtl.com/zh/vue/第一个-Vue-应用程序.html#第一个-vue-应用程序-2)第一个 Vue 应用程序

Vue.js 的核心是实现了 MVVM 模式，她扮演的角色就是 ViewModel 层，那么所谓的第一个应用程序就是展示她的 **数据绑定** 功能，操作流程如下：

### [#](https://funtl.com/zh/vue/第一个-Vue-应用程序.html#创建一个-html-文件)创建一个 HTML 文件

```html
<!DOCTYPE html>
<html>
<head>
    <meta charset="UTF-8">
    <title>第一个 Vue 应用程序</title>
</head>
<body>

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

### [#](https://funtl.com/zh/vue/第一个-Vue-应用程序.html#引入-vue-js)引入 Vue.js

```html
<script src="https://cdn.jsdelivr.net/npm/vue@2.5.21/dist/vue.js"></script>
```

1

### [#](https://funtl.com/zh/vue/第一个-Vue-应用程序.html#创建一个-vue-的实例)创建一个 Vue 的实例

```javascript
<script type="text/javascript">
    var vm = new Vue({
        el: '#vue',
        data: {
            message: 'Hello Vue!'
        }
    });
</script>
```

1
2
3
4
5
6
7
8

#### [#](https://funtl.com/zh/vue/第一个-Vue-应用程序.html#说明)说明

- `el:'#vue'`：绑定元素的 ID
- `data:{message:'Hello Vue!'}`：数据对象中有一个名为 `message` 的属性，并设置了初始值 `Hello Vue!`

### [#](https://funtl.com/zh/vue/第一个-Vue-应用程序.html#将数据绑定到页面元素)将数据绑定到页面元素

```html
<div id="vue">
    {{message}}
</div>
```

1
2
3

说明：只需要在绑定的元素中使用 `双花括号` 将 Vue 创建的名为 `message` 属性包裹起来，即可实现数据绑定功能，也就实现了 ViewModel 层所需的效果，是不是和 `EL` 表达式非常像？

```text
#{message} => {{message}}
```

1

### [#](https://funtl.com/zh/vue/第一个-Vue-应用程序.html#完整的-html)完整的 HTML

```html
<!DOCTYPE html>
<html>
<head>
    <meta charset="UTF-8">
    <title>第一个 Vue 应用程序</title>
    <script src="https://cdn.jsdelivr.net/npm/vue@2.5.21/dist/vue.js"></script>
</head>
<body>
    <div id="vue">
        {{message}}
    </div>

<script type="text/javascript">
    var vm = new Vue({
        el: '#vue',
        data: {
            message: 'Hello Vue!'
        }
    });
</script>
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
14
15
16
17
18
19
20
21
22

注：我是在 `IDEA` 上创建的 HTML，并使用 `IDEA` 内置的 HTTP 服务器运行

## [#](https://funtl.com/zh/vue/第一个-Vue-应用程序.html#测试-vue)测试 Vue

为了能够更直观的体验 Vue 带来的数据绑定功能，我们需要在浏览器测试一番，操作流程如下：

- 在 `Chrome` 浏览器上运行第一个 Vue 应用程序，并按 `F12` 进入 `开发者工具`

![img](https://funtl.com/assets/Lusifer_20181217214321.png)

- 在控制台输入 `vm.message = 'Hello World'` ，然后 `回车`，你会发现浏览器中显示的内容会直接变成 `Hello World`

![img](https://funtl.com/assets/Lusifer_20181217214803.png)

### [#](https://funtl.com/zh/vue/第一个-Vue-应用程序.html#说明-2)说明

在之前的代码中，我们创建了一个名为 `vm` 的 Vue 实例

```javascript
var vm = new Vue({
    el: '#vue',
    data: {
        message: 'Hello Vue!'
    }
});
```

1
2
3
4
5
6

此时就可以在控制台直接输入 `vm.message` 来修改值，中间是可以省略 `data` 的，在这个操作中，我并没有主动操作 DOM，就让页面的内容发生了变化，这就是借助了 Vue 的 **数据绑定** 功能实现的；MVVM 模式中要求 ViewModel 层就是使用 `观察者模式` 来实现数据的监听与绑定，以做到数据与视图的快速响应。

上次更新: 2019-4-19 19:23:15

← [什么是 Vue](https://funtl.com/zh/vue/)[附：Vue 实例的生命周期 ](https://funtl.com/zh/vue/附：Vue-实例的生命周期.html)→