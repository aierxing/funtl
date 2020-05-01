# [#](https://funtl.com/zh/apache-dubbo-rpc/Dubbo-的组件角色.html#dubbo-的组件角色)Dubbo 的组件角色

## [#](https://funtl.com/zh/apache-dubbo-rpc/Dubbo-的组件角色.html#本节视频)本节视频

- [【视频】Dubbo 实现微服务架构-Dubbo-核心功能与组件角色](https://www.bilibili.com/video/av34187440/)

## [#](https://funtl.com/zh/apache-dubbo-rpc/Dubbo-的组件角色.html#概述)概述

![img](https://funtl.com/assets1/687474703a2f2f647562626f2e6170616368652e6f72672f696d672f6172636869746563747572652e706e67.png)

| 组件角色  | 说明                                   |
| :-------- | :------------------------------------- |
| Provider  | 暴露服务的服务提供方                   |
| Consumer  | 调用远程服务的服务消费方               |
| Registry  | 服务注册与发现的注册中心               |
| Monitor   | 统计服务的调用次调和调用时间的监控中心 |
| Container | 服务运行容器                           |

**调用关系说明：**

- 服务容器 `Container` 负责启动，加载，运行服务提供者。
- 服务提供者 `Provider` 在启动时，向注册中心注册自己提供的服务。
- 服务消费者 `Consumer` 在启动时，向注册中心订阅自己所需的服务。
- 注册中心 `Registry` 返回服务提供者地址列表给消费者，如果有变更，注册中心将基于长连接推送变更数据给消费者。
- 服务消费者 `Consumer`，从提供者地址列表中，基于软负载均衡算法，选一台提供者进行调用，如果调用失败，再选另一台调用。
- 服务消费者 `Consumer` 和提供者 `Provider`，在内存中累计调用次数和调用时间，定时每分钟发送一次统计数据到监控中心 `Monitor`。

上次更新: 2019-2-3 12:21:36

← [Dubbo 的核心功能](https://funtl.com/zh/apache-dubbo-rpc/Dubbo-的核心功能.html)[Dubbo Admin 管理控制台 ](https://funtl.com/zh/apache-dubbo-rpc/Dubbo-Admin-管理控制台.html)→