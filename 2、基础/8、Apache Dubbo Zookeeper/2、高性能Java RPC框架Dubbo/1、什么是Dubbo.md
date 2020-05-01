# 什么是 Dubbo

## [#](https://funtl.com/zh/apache-dubbo-rpc/#本节视频)本节视频

- [【视频】Dubbo 实现微服务架构-Dubbo-简介1](https://www.bilibili.com/video/av34187218/)
- [【视频】Dubbo 实现微服务架构-Dubbo-简介2](https://www.bilibili.com/video/av34187257/)

## [#](https://funtl.com/zh/apache-dubbo-rpc/#概述)概述

Apache Dubbo (incubating) |ˈdʌbəʊ| 是一款高性能、轻量级的开源 Java RPC 分布式服务框架，它提供了三大核心能力：面向接口的远程方法调用，智能容错和负载均衡，以及服务自动注册和发现。她最大的特点是按照分层的方式来架构，使用这种方式可以使各个层之间解耦合（或者最大限度地松耦合）。从服务模型的角度来看，Dubbo 采用的是一种非常简单的模型，要么是提供方提供服务，要么是消费方消费服务，所以基于这一点可以抽象出服务提供方（Provider）和服务消费方（Consumer）两个角色。

- 官网：http://dubbo.apache.org/zh-cn
- GitHub：https://github.com/apache/incubator-dubbo

上次更新: 2018-12-31 18:51:48

[Dubbo 的服务治理 ](https://funtl.com/zh/apache-dubbo-rpc/Dubbo-的服务治理.html)→