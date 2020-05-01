# [#](https://funtl.com/zh/vue-router/第一个-ElementUI-页面.html#第一个-elementui-页面-登录页)第一个 ElementUI 页面 (登录页)

## [#](https://funtl.com/zh/vue-router/第一个-ElementUI-页面.html#本节视频)本节视频

- [【视频】Vue 渐进式 JavaScript 框架-VueRouter-创建登录页](https://www.bilibili.com/video/av43994228/)

## [#](https://funtl.com/zh/vue-router/第一个-ElementUI-页面.html#目录结构)目录结构

在源码目录中创建如下结构：

- assets：用于存放资源文件
- components：用于存放 Vue 功能组件
- views：用于存放 Vue 视图组件
- router：用于存放 vue-router 配置

![img](https://funtl.com/assets1/Lusifer_20190217012252.png)

## [#](https://funtl.com/zh/vue-router/第一个-ElementUI-页面.html#创建视图)创建视图

### [#](https://funtl.com/zh/vue-router/第一个-ElementUI-页面.html#创建首页视图)创建首页视图

在 `views` 目录下创建一个名为 `Main.vue` 的视图组件；该组件在当前章节无任何作用，主要用于登录后展示登录成功的跳转效果；

```vue
<template>
    <div>
      首页
    </div>
</template>

<script>
    export default {
        name: "Main"
    }
</script>

<style scoped>

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

### [#](https://funtl.com/zh/vue-router/第一个-ElementUI-页面.html#创建登录页视图)创建登录页视图

在 `views` 目录下创建一个名为 `Login.vue` 的视图组件，其中 `el-*` 的元素为 ElementUI 组件；

```vue
<template>
  <div>
    <el-form ref="loginForm" :model="form" :rules="rules" label-width="80px" class="login-box">
      <h3 class="login-title">欢迎登录</h3>
      <el-form-item label="账号" prop="username">
        <el-input type="text" placeholder="请输入账号" v-model="form.username"/>
      </el-form-item>
      <el-form-item label="密码" prop="password">
        <el-input type="password" placeholder="请输入密码" v-model="form.password"/>
      </el-form-item>
      <el-form-item>
        <el-button type="primary" v-on:click="onSubmit('loginForm')">登录</el-button>
      </el-form-item>
    </el-form>

    <el-dialog
      title="温馨提示"
      :visible.sync="dialogVisible"
      width="30%"
      :before-close="handleClose">
      <span>请输入账号和密码</span>
      <span slot="footer" class="dialog-footer">
        <el-button type="primary" @click="dialogVisible = false">确 定</el-button>
      </span>
    </el-dialog>
  </div>
</template>

<script>
  export default {
    name: "Login",
    data() {
      return {
        form: {
          username: '',
          password: ''
        },

        // 表单验证，需要在 el-form-item 元素中增加 prop 属性
        rules: {
          username: [
            {required: true, message: '账号不可为空', trigger: 'blur'}
          ],
          password: [
            {required: true, message: '密码不可为空', trigger: 'blur'}
          ]
        },

        // 对话框显示和隐藏
        dialogVisible: false
      }
    },
    methods: {
      onSubmit(formName) {
        // 为表单绑定验证功能
        this.$refs[formName].validate((valid) => {
          if (valid) {
            // 使用 vue-router 路由到指定页面，该方式称之为编程式导航
            this.$router.push("/main");
          } else {
            this.dialogVisible = true;
            return false;
          }
        });
      }
    }
  }
</script>

<style lang="scss" scoped>
  .login-box {
    border: 1px solid #DCDFE6;
    width: 350px;
    margin: 180px auto;
    padding: 35px 35px 15px 35px;
    border-radius: 5px;
    -webkit-border-radius: 5px;
    -moz-border-radius: 5px;
    box-shadow: 0 0 25px #909399;
  }

  .login-title {
    text-align: center;
    margin: 0 auto 40px auto;
    color: #303133;
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
42
43
44
45
46
47
48
49
50
51
52
53
54
55
56
57
58
59
60
61
62
63
64
65
66
67
68
69
70
71
72
73
74
75
76
77
78
79
80
81
82
83
84
85
86
87

## [#](https://funtl.com/zh/vue-router/第一个-ElementUI-页面.html#创建路由)创建路由

在 `router` 目录下创建一个名为 `index.js` 的 vue-router 路由配置文件

```javascript
import Vue from 'vue'
import Router from 'vue-router'

import Login from "../views/Login"
import Main from '../views/Main'

Vue.use(Router);

export default new Router({
  routes: [
    {
      // 登录页
      path: '/login',
      name: 'Login',
      component: Login
    },
    {
      // 首页
      path: '/main',
      name: 'Main',
      component: Main
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
23
24

## [#](https://funtl.com/zh/vue-router/第一个-ElementUI-页面.html#配置路由)配置路由

### [#](https://funtl.com/zh/vue-router/第一个-ElementUI-页面.html#修改入口代码)修改入口代码

修改 `main.js` 入口代码

```javascript
import Vue from 'vue'
import VueRouter from 'vue-router'
import router from './router'

// 导入 ElementUI
import ElementUI from 'element-ui'
import 'element-ui/lib/theme-chalk/index.css'

import App from './App'

// 安装路由
Vue.use(VueRouter);

// 安装 ElementUI
Vue.use(ElementUI);

new Vue({
  el: '#app',
  // 启用路由
  router,
  // 启用 ElementUI
  render: h => h(App)
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
23

修改 `App.vue` 组件代码

```vue
<template>
  <div id="app">
    <router-view/>
  </div>
</template>

<script>
  export default {
    name: 'App',
  }
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

## [#](https://funtl.com/zh/vue-router/第一个-ElementUI-页面.html#效果演示)效果演示

在浏览器打开 http://localhost:8080/#/login 你会看到如下效果

![img](https://funtl.com/assets1/Lusifer_201902170001.gif)

上次更新: 2019-2-19 3:34:24

← [第一个 Vue 工程项目](https://funtl.com/zh/vue-router/第一个-Vue-工程项目.html)[配置嵌套路由 ](https://funtl.com/zh/vue-router/配置嵌套路由.html)→