# [#](https://funtl.com/zh/apache-dubbo-rpc/Dubbo-的核心功能.html#dubbo-的核心功能)Dubbo 的核心功能

## [#](https://funtl.com/zh/apache-dubbo-rpc/Dubbo-的核心功能.html#本节视频)本节视频

- [【视频】Dubbo 实现微服务架构-Dubbo-核心功能与组件角色](https://www.bilibili.com/video/av34187440/)

## [#](https://funtl.com/zh/apache-dubbo-rpc/Dubbo-的核心功能.html#概述)概述

- Remoting：远程通讯，提供对多种 NIO 框架抽象封装，包括“同步转异步”和“请求-响应”模式的信息交换方式。
- Cluster：服务框架，提供基于接口方法的透明远程过程调用，包括多协议支持，以及软负载均衡，失败容错，地址路由，动态配置等集群支持。
- Registry：服务注册中心，服务自动发现: 基于注册中心目录服务，使服务消费方能动态的查找服务提供方，使地址透明，使服务提供方可以平滑增加或减少机器。

上次更新: 2018-12-31 18:51:48

← [Dubbo 的服务治理](https://funtl.com/zh/apache-dubbo-rpc/Dubbo-的服务治理.html)[Dubbo 的组件角色 ](https://funtl.com/zh/apache-dubbo-rpc/Dubbo-的组件角色.html)→