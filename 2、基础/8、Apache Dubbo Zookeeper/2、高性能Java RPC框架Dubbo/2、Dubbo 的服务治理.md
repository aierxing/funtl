# [#](https://funtl.com/zh/apache-dubbo-rpc/Dubbo-的服务治理.html#dubbo-的服务治理)Dubbo 的服务治理

## [#](https://funtl.com/zh/apache-dubbo-rpc/Dubbo-的服务治理.html#本节视频)本节视频

- [【视频】Dubbo 实现微服务架构-Dubbo-服务治理](https://www.bilibili.com/video/av34187294/)

## [#](https://funtl.com/zh/apache-dubbo-rpc/Dubbo-的服务治理.html#概述)概述

![img](https://funtl.com/assets/2062729-5f97eab81f9d55cb.png)

| 特性         | 描述                                                         |
| :----------- | :----------------------------------------------------------- |
| 透明远程调用 | 就像调用本地方法一样调用远程方法；只需简单配置，没有任何 API 侵入 |
| 负载均衡机制 | Client 端 LB，可在内网替代 F5 等硬件负载均衡器               |
| 容错重试机制 | 服务 Mock 数据，重试次数、超时机制等                         |
| 自动注册发现 | 注册中心基于接口名查询服务提 供者的 IP 地址，并且能够平滑添加或删除服务提供者 |
| 性能日志监控 | Monitor 统计服务的调用次调和调用时间的监控中心               |
| 服务治理中心 | 路由规则，动态配置，服务降级，访问控制，权重调整，负载均衡，等手动配置 |
| 自动治理中心 | 无，比如：熔断限流机制、自动权重调整等                       |

上次更新: 2019-4-19 19:23:15

← [什么是 Dubbo](https://funtl.com/zh/apache-dubbo-rpc/)[Dubbo 的核心功能 ](https://funtl.com/zh/apache-dubbo-rpc/Dubbo-的核心功能.html)→