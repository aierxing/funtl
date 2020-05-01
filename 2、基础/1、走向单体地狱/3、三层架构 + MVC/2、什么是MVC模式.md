# 什么是 MVC 模式

## [#](https://funtl.com/zh/mvc/什么是-MVC-模式.html#本节视频)本节视频

- [【视频】三层架构+MVC-什么是 MVC 模式](https://www.bilibili.com/video/av24480269/)

## [#](https://funtl.com/zh/mvc/什么是-MVC-模式.html#概述)概述

MVC，即 Model 模型、View 视图，及 Controller 控制器。

- View：视图，为用户提供使用界面，与用户直接进行交互。
- Model：模型，承载数据，并对用户提交请求进行计算的模块。其分为两类，一类称为数据承载 Bean，一类称为业务处理 Bean。所谓数据承载 Bean 是指实体类，专门用户承载业务数据的，如 Student、User 等。而业务处理 Bean 则是指 Service 或 Dao 对象， 专门用于处理用户提交请求的。
- Controller：控制器，用于将用户请求转发给相应的 Model 进行处理，并根据 Model 的计算结果向用户提供相应响应。

## [#](https://funtl.com/zh/mvc/什么是-MVC-模式.html#mvc-架构程序的工作流程)MVC 架构程序的工作流程

- 用户通过 View 页面向服务端提出请求，可以是表单请求、超链接请求、AJAX 请求等
- 服务端 Controller 控制器接收到请求后对请求进行解析，找到相应的 Model 对用户请求进行处理
- Model 处理后，将处理结果再交给 Controller
- Controller 在接到处理结果后，根据处理结果找到要作为向客户端发回的响应 View 页面。页面经渲染（数据填充）后，再发送给客户端。

![img](https://funtl.com/assets/Lusifer2018060421090002.png)

## [#](https://funtl.com/zh/mvc/什么是-MVC-模式.html#三层架构-mvc-示意图)三层架构 + MVC 示意图

![img](https://funtl.com/assets/Lusifer2018060421090003.png)

上次更新: 2019-4-19 19:23:15