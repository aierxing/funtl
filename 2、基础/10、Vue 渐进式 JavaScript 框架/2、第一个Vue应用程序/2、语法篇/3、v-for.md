# [#](https://funtl.com/zh/vue/v-for.html#v-for)v-for

## [#](https://funtl.com/zh/vue/v-for.html#本节视频)本节视频

- [【视频】Vue 渐进式 JavaScript 框架-Vue-语法篇-for](https://www.bilibili.com/video/av43628585/)

## [#](https://funtl.com/zh/vue/v-for.html#循环遍历语句)循环遍历语句

- `v-for`

## [#](https://funtl.com/zh/vue/v-for.html#html)HTML

```html
<div id="vue">
    <li v-for="item in items">
        {{ item.message }}
    </li>
</div>
```

1
2
3
4
5

注：`items` 是源数据数组并且 `item` 是数组元素迭代的别名。是不是像极了 `Thymeleaf`

## [#](https://funtl.com/zh/vue/v-for.html#javascript)JavaScript

```javascript
<script type="text/javascript">
    var vm = new Vue({
        el: '#vue',
        data: {
            items: [
                {message: 'Foo'},
                {message: 'Bar'}
            ]
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

## [#](https://funtl.com/zh/vue/v-for.html#测试效果)测试效果

- 在 `Chrome` 浏览器上运行，并按 `F12` 进入 `开发者工具`

![img](https://funtl.com/assets/Lusifer_20181218213603.png)

- 在控制台输入 `vm.items.push({message: 'Baz'})` ，尝试追加一条数据，你会发现浏览器中显示的内容会增加一条 `Baz`

![img](https://funtl.com/assets/Lusifer_20181218213834.png)

## [#](https://funtl.com/zh/vue/v-for.html#完整的-html)完整的 HTML

```html
<!DOCTYPE html>
<html>
<head>
    <meta charset="UTF-8">
    <title>语法篇 v-for</title>
    <script src="https://cdn.jsdelivr.net/npm/vue@2.5.21/dist/vue.js"></script>
</head>
<body>

<div id="vue">
    <li v-for="item in items">
        {{ item.message }}
    </li>
</div>

<script type="text/javascript">
    var vm = new Vue({
        el: '#vue',
        data: {
            items: [
                {message: 'Foo'},
                {message: 'Bar'}
            ]
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

上次更新: 2019-4-19 19:23:15

← [v-else-if](https://funtl.com/zh/vue/v-else-if.html)[v-on ](https://funtl.com/zh/vue/v-on.html)→