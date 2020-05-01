# [#](https://funtl.com/zh/vue/v-on.html#v-on)v-on

## [#](https://funtl.com/zh/vue/v-on.html#本节视频)本节视频

- [【视频】Vue 渐进式 JavaScript 框架-Vue-事件篇-on](https://www.bilibili.com/video/av43628700/)

## [#](https://funtl.com/zh/vue/v-on.html#监听事件)监听事件

- `v-on`

## [#](https://funtl.com/zh/vue/v-on.html#html)HTML

```html
<div id="vue">
    <button v-on:click="sayHi">点我</button>
</div>
```

1
2
3

注：在这里我们使用了 `v-on` 绑定了 `click` 事件，并指定了名为 `sayHi` 的方法

## [#](https://funtl.com/zh/vue/v-on.html#javascript)JavaScript

方法必须定义在 Vue 实例的 `methods` 对象中

```javascript
<script type="text/javascript">
    var vm = new Vue({
        el: '#vue',
        data: {
            message: 'Hello World'
        },
        // 在 `methods` 对象中定义方法
        methods: {
            sayHi: function (event) {
                // `this` 在方法里指向当前 Vue 实例
                alert(this.message);
            }
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
9
10
11
12
13
14
15

## [#](https://funtl.com/zh/vue/v-on.html#测试效果)测试效果

![img](https://funtl.com/assets/Lusifer_20181219014947.png)

## [#](https://funtl.com/zh/vue/v-on.html#完整的-html)完整的 HTML

```html
<!DOCTYPE html>
<html xmlns:v-on="">
<head>
    <meta charset="UTF-8">
    <title>事件篇 v-on</title>
    <script src="https://cdn.jsdelivr.net/npm/vue@2.5.21/dist/vue.js"></script>
</head>
<body>

<div id="vue">
    <button v-on:click="sayHi">点我</button>
</div>

<script type="text/javascript">
    var vm = new Vue({
        el: '#vue',
        data: {
            message: 'Hello World'
        },
        // 在 `methods` 对象中定义方法
        methods: {
            sayHi: function (event) {
                // `this` 在方法里指向当前 Vue 实例
                alert(this.message);
            }
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
23
24
25
26
27
28
29
30

上次更新: 2019-4-19 19:23:15

← [v-for](https://funtl.com/zh/vue/v-for.html)[使用 Axios 实现异步通信 ](https://funtl.com/zh/vue/使用-Axios-实现异步通信.html)→