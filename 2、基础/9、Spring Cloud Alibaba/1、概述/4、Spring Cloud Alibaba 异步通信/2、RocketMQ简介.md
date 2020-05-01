# [#](https://funtl.com/zh/spring-cloud-alibaba/RocketMQ-简介.html#rocketmq-简介)RocketMQ 简介

## [#](https://funtl.com/zh/spring-cloud-alibaba/RocketMQ-简介.html#概述)概述

消息队列作为高并发系统的核心组件之一，能够帮助业务系统解构提升开发效率和系统稳定性。主要具有以下优势：

- **削峰填谷：** 主要解决瞬时写压力大于应用服务能力导致消息丢失、系统奔溃等问题
- **系统解耦：** 解决不同重要程度、不同能力级别系统之间依赖导致一死全死
- **提升性能：** 当存在一对多调用时，可以发一条消息给消息系统，让消息系统通知相关系统
- **蓄流压测：** 线上有些链路不好压测，可以通过堆积一定量消息再放开来压测

## [#](https://funtl.com/zh/spring-cloud-alibaba/RocketMQ-简介.html#rocketmq)RocketMQ

Apache Alibaba RocketMQ 是一个消息中间件。消息中间件中有两个角色：消息生产者和消息消费者。RocketMQ 里同样有这两个概念，消息生产者负责创建消息并发送到 RocketMQ 服务器，RocketMQ 服务器会将消息持久化到磁盘，消息消费者从 RocketMQ 服务器拉取消息并提交给应用消费。

## [#](https://funtl.com/zh/spring-cloud-alibaba/RocketMQ-简介.html#rocketmq-特点)RocketMQ 特点

RocketMQ 是一款分布式、队列模型的消息中间件，具有以下特点：

- 支持严格的消息顺序
- 支持 Topic 与 Queue 两种模式
- 亿级消息堆积能力
- 比较友好的分布式特性
- 同时支持 Push 与 Pull 方式消费消息
- **历经多次天猫双十一海量消息考验**

## [#](https://funtl.com/zh/spring-cloud-alibaba/RocketMQ-简介.html#rocketmq-优势)RocketMQ 优势

目前主流的 MQ 主要是 RocketMQ、kafka、RabbitMQ，其主要优势有：

- 支持事务型消息（消息发送和 DB 操作保持两方的最终一致性，RabbitMQ 和 Kafka 不支持）
- 支持结合 RocketMQ 的多个系统之间数据最终一致性（多方事务，二方事务是前提）
- 支持 18 个级别的延迟消息（RabbitMQ 和 Kafka 不支持）
- 支持指定次数和时间间隔的失败消息重发（Kafka 不支持，RabbitMQ 需要手动确认）
- 支持 Consumer 端 Tag 过滤，减少不必要的网络传输（RabbitMQ 和 Kafka 不支持）
- 支持重复消费（RabbitMQ 不支持，Kafka 支持）

## [#](https://funtl.com/zh/spring-cloud-alibaba/RocketMQ-简介.html#消息队列对比参照表)消息队列对比参照表

![img](https://funtl.com/assets1/12619159-ebd12b24d5ae33d9.png)

上次更新: 2019-1-15 2:45:51

← [消息队列的流派](https://funtl.com/zh/spring-cloud-alibaba/消息队列的流派.html)[基于 Docker 安装 RocketMQ ](https://funtl.com/zh/spring-cloud-alibaba/基于-Docker-安装-RocketMQ.html)→