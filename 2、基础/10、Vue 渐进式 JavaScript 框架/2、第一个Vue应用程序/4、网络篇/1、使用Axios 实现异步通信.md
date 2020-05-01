# [#](https://funtl.com/zh/vue/使用-Axios-实现异步通信.html#使用-axios-实现异步通信)使用 Axios 实现异步通信

## [#](https://funtl.com/zh/vue/使用-Axios-实现异步通信.html#本节视频)本节视频

- [【视频】Vue 渐进式 JavaScript 框架-Vue-网络篇-axios](https://www.bilibili.com/video/av43628773/)

## [#](https://funtl.com/zh/vue/使用-Axios-实现异步通信.html#什么是-axios)什么是 Axios

Axios 是一个开源的可以用在浏览器端和 NodeJS 的异步通信框架，她的主要作用就是实现 AJAX 异步通信，其功能特点如下：

- 从浏览器中创建 `XMLHttpRequests`
- 从 `node.js` 创建 `http` 请求
- 支持 `Promise API`
- 拦截请求和响应
- 转换请求数据和响应数据
- 取消请求
- 自动转换 `JSON` 数据
- 客户端支持防御 `XSRF`（跨站请求伪造）

GitHub：https://github.com/axios/axios

## [#](https://funtl.com/zh/vue/使用-Axios-实现异步通信.html#为什么要使用-axios)为什么要使用 Axios

由于 Vue.js 是一个 **视图层框架** 并且作者（尤雨溪）严格准守 `SoC` （关注度分离原则），所以 Vue.js 并不包含 AJAX 的通信功能，为了解决通信问题，作者单独开发了一个名为 `vue-resource` 的插件，不过在进入 2.0 版本以后停止了对该插件的维护并推荐了 Axios 框架

## [#](https://funtl.com/zh/vue/使用-Axios-实现异步通信.html#第一个-axios-应用程序)第一个 Axios 应用程序

咱们开发的接口大部分都是采用 JSON 格式，可以先在项目里模拟一段 JSON 数据，数据内容如下：

```json
{
  "name": "广州千锋",
  "url": "http://www.funtl.com",
  "page": 88,
  "isNonProfit": true,
  "address": {
    "street": "元岗路.",
    "city": "广东广州",
    "country": "中国"
  },
  "links": [
    {
      "name": "Google",
      "url": "http://www.google.com"
    },
    {
      "name": "Baidu",
      "url": "http://www.baidu.com"
    },
    {
      "name": "SoSo",
      "url": "http://www.SoSo.com"
    }
  ]
}
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

创建一个名为 `data.json` 的文件并填入上面的内容，放在项目的根目录下，如图所示：

![img](https://funtl.com/assets/Lusifer_20181219031007.png)

### [#](https://funtl.com/zh/vue/使用-Axios-实现异步通信.html#创建-html)创建 HTML

```html
<div id="vue">
    <div>名称：{{info.name}}</div>
    <div>地址：{{info.address.country}}-{{info.address.city}}-{{info.address.street}}</div>
    <div>链接：<a v-bind:href="info.url" target="_blank">{{info.url}}</a> </div>
</div>
```

1
2
3
4
5

注：在这里使用了 `v-bind` 将 `a:href` 的属性值与 Vue 实例中的数据进行绑定

### [#](https://funtl.com/zh/vue/使用-Axios-实现异步通信.html#引入-js-文件)引入 JS 文件

```html
<script src="https://cdn.jsdelivr.net/npm/vue@2.5.21/dist/vue.js"></script>
<script src="https://unpkg.com/axios/dist/axios.min.js"></script>
```

1
2

### [#](https://funtl.com/zh/vue/使用-Axios-实现异步通信.html#javascript)JavaScript

```javascript
<script type="text/javascript">
    var vm = new Vue({
        el: '#vue',
        data() {
            return {
                info: {
                    name: null,
                    address: {
                        country: null,
                        city: null,
                        street: null
                    },
                    url: null
                }
            }
        },
        mounted() {
            axios
                .get('data.json')
                .then(response => (this.info = response.data));
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
16
17
18
19
20
21
22
23

使用 `axios` 框架的 `get` 方法请求 AJAX 并自动将数据封装进了 Vue 实例的数据对象中

#### [#](https://funtl.com/zh/vue/使用-Axios-实现异步通信.html#数据对象)数据对象

这里的数据结构与 JSON 数据结构是匹配的

```json
info: {
    name: null,
    address: {
        country: null,
        city: null,
        street: null
    },
    url: null
}
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

#### [#](https://funtl.com/zh/vue/使用-Axios-实现异步通信.html#调用-get-请求)调用 `get` 请求

调用 `axios` 的 `get` 请求并自动装箱数据

```javascript
axios
    .get('data.json')
    .then(response => (this.info = response.data));
```

1
2
3

### [#](https://funtl.com/zh/vue/使用-Axios-实现异步通信.html#测试效果)测试效果

![img](https://funtl.com/assets/Lusifer_20181219035421.png)

### [#](https://funtl.com/zh/vue/使用-Axios-实现异步通信.html#完整的-html)完整的 HTML

```html
<!DOCTYPE html>
<html>
<head>
    <meta charset="UTF-8">
    <title>网络篇 Axios</title>
    <script src="https://cdn.jsdelivr.net/npm/vue@2.5.21/dist/vue.js"></script>
    <script src="https://unpkg.com/axios/dist/axios.min.js"></script>
</head>
<body>

<div id="vue">
    <div>名称：{{info.name}}</div>
    <div>地址：{{info.address.country}}-{{info.address.city}}-{{info.address.street}}</div>
    <div>链接：<a v-bind:href="info.url" target="_blank">{{info.url}}</a> </div>
</div>

<script type="text/javascript">
    var vm = new Vue({
        el: '#vue',
        data() {
            return {
                info: {
                    name: null,
                    address: {
                        country: null,
                        city: null,
                        street: null
                    },
                    url: null
                }
            }
        },
        mounted() {
            axios
                .get('data.json')
                .then(response => (this.info = response.data));
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
31
32
33
34
35
36
37
38
39
40
41

上次更新: 2019-4-19 19:23:15

← [v-on](https://funtl.com/zh/vue/v-on.html)[表单输入 ](https://funtl.com/zh/vue/表单输入.html)→