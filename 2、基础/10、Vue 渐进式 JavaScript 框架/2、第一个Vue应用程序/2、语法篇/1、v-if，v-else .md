# [#](https://funtl.com/zh/vue/v-if-v-else.html#v-if-v-else)v-if,v-else

## [#](https://funtl.com/zh/vue/v-if-v-else.html#本节视频)本节视频

- [【视频】Vue 渐进式 JavaScript 框架-Vue-语法篇-ifelse](https://www.bilibili.com/video/av43628478/)

## [#](https://funtl.com/zh/vue/v-if-v-else.html#条件判断语句)条件判断语句

- `v-if`
- `v-else`

什么是条件判断语句，就不需要我说明了吧（￣▽￣），直接看语法上效果

## [#](https://funtl.com/zh/vue/v-if-v-else.html#html)HTML

```html
<div id="vue">
    <h1 v-if="ok">YES</h1>
    <h1 v-else>NO</h1>
</div>
```

1
2
3
4

## [#](https://funtl.com/zh/vue/v-if-v-else.html#javascript)JavaScript

```javascript
<script type="text/javascript">
    var vm = new Vue({
        el: '#vue',
        data: {
            ok: true
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

## [#](https://funtl.com/zh/vue/v-if-v-else.html#测试效果)测试效果

- 在 `Chrome` 浏览器上运行，并按 `F12` 进入 `开发者工具`

![img](https://funtl.com/assets/Lusifer_20181218033118.png)

- 在控制台输入 `vm.ok = false` ，然后 `回车`，你会发现浏览器中显示的内容会直接变成 `NO`

![img](https://funtl.com/assets/Lusifer_20181218033338.png)

注：使用 `v-*` 属性绑定数据是不需要 `双花括号` 包裹的

## [#](https://funtl.com/zh/vue/v-if-v-else.html#完整的-html)完整的 HTML

```html
<!DOCTYPE html>
<html>
<head>
    <meta charset="UTF-8">
    <title>语法篇 v-if</title>
    <script src="https://cdn.jsdelivr.net/npm/vue@2.5.21/dist/vue.js"></script>
</head>
<body>

<div id="vue">
    <h1 v-if="ok">YES</h1>
    <h1 v-else>NO</h1>
</div>

<script type="text/javascript">
    var vm = new Vue({
        el: '#vue',
        data: {
            ok: true
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

上次更新: 2019-4-19 19:23:15

← [附：Vue 实例的生命周期](https://funtl.com/zh/vue/附：Vue-实例的生命周期.html)[v-else-if ](https://funtl.com/zh/vue/v-else-if.html)→