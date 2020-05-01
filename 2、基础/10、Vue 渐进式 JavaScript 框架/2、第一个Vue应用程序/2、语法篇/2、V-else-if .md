# [#](https://funtl.com/zh/vue/v-else-if.html#v-else-if)v-else-if

## [#](https://funtl.com/zh/vue/v-else-if.html#本节视频)本节视频

- [【视频】Vue 渐进式 JavaScript 框架-Vue-语法篇-elseif](https://www.bilibili.com/video/av43628537/)

## [#](https://funtl.com/zh/vue/v-else-if.html#连续的条件判断语句)连续的条件判断语句

- `v-if`
- `v-else-if`
- `v-else`

## [#](https://funtl.com/zh/vue/v-else-if.html#html)HTML

```html
<div id="vue">
    <h1 v-if="type === 'A'">A</h1>
    <h1 v-else-if="type === 'B'">B</h1>
    <h1 v-else-if="type === 'C'">C</h1>
    <h1 v-else>你看不见我</h1>
</div>
```

1
2
3
4
5
6

注：`===` 三个等号在 JS 中表示绝对等于（就是数据与类型都要相等）

## [#](https://funtl.com/zh/vue/v-else-if.html#javascript)JavaScript

```javascript
<script type="text/javascript">
    var vm = new Vue({
        el: '#vue',
        data: {
            type: 'A'
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

## [#](https://funtl.com/zh/vue/v-else-if.html#测试效果)测试效果

- 在 `Chrome` 浏览器上运行，并按 `F12` 进入 `开发者工具`

![img](https://funtl.com/assets/Lusifer_20181218034852.png)

- 分别观察在控制台输入 `vm.type = 'B'、'C'、'D'` 的变化

![img](https://funtl.com/assets/Lusifer_20181218035036.png)

## [#](https://funtl.com/zh/vue/v-else-if.html#完整的-html)完整的 HTML

```html
<!DOCTYPE html>
<html>
<head>
    <meta charset="UTF-8">
    <title>语法篇 v-else-if</title>
    <script src="https://cdn.jsdelivr.net/npm/vue@2.5.21/dist/vue.js"></script>
</head>
<body>

<div id="vue">
    <h1 v-if="type === 'A'">A</h1>
    <h1 v-else-if="type === 'B'">B</h1>
    <h1 v-else-if="type === 'C'">C</h1>
    <h1 v-else>你看不见我</h1>
</div>

<script type="text/javascript">
    var vm = new Vue({
        el: '#vue',
        data: {
            type: 'A'
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

上次更新: 2019-4-19 19:23:15

← [v-if,v-else](https://funtl.com/zh/vue/v-if-v-else.html)[v-for ](https://funtl.com/zh/vue/v-for.html)→