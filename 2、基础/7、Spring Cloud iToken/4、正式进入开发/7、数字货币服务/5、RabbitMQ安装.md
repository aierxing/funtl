# [#](https://funtl.com/zh/spring-cloud-itoken-codeing/RabbitMQ-安装.html#rabbitmq-安装)RabbitMQ 安装

## [#](https://funtl.com/zh/spring-cloud-itoken-codeing/RabbitMQ-安装.html#本节视频)本节视频

- [【视频】项目实战-iToken-消息队列-RabbitMQ 安装](https://www.bilibili.com/video/av29725878)

## [#](https://funtl.com/zh/spring-cloud-itoken-codeing/RabbitMQ-安装.html#概述)概述

我们基于 Docker 来安装 RabbitMQ

## [#](https://funtl.com/zh/spring-cloud-itoken-codeing/RabbitMQ-安装.html#docker-compose-yml)docker-compose.yml

```text
version: '3.1'
services:
  rabbitmq:
    restart: always
    image: rabbitmq:management
    container_name: rabbitmq
    ports:
      - 5672:5672
      - 15672:15672
    environment:
      TZ: Asia/Shanghai
      RABBITMQ_DEFAULT_USER: rabbit
      RABBITMQ_DEFAULT_PASS: 123456
    volumes:
      - ./data:/var/lib/rabbitmq
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

## [#](https://funtl.com/zh/spring-cloud-itoken-codeing/RabbitMQ-安装.html#rabbitmq-webui)RabbitMQ WebUI

### [#](https://funtl.com/zh/spring-cloud-itoken-codeing/RabbitMQ-安装.html#访问地址)访问地址

http://ip:15672

### [#](https://funtl.com/zh/spring-cloud-itoken-codeing/RabbitMQ-安装.html#首页)首页

![img](https://funtl.com/assets/Lusifer2018050122030001.png)

### [#](https://funtl.com/zh/spring-cloud-itoken-codeing/RabbitMQ-安装.html#global-counts)Global counts

![img](https://funtl.com/assets/Lusifer2018050122030002.png)

- Connections：连接数
- Channels：频道数
- Exchanges：交换机数
- Queues：队列数
- Consumers：消费者数

### [#](https://funtl.com/zh/spring-cloud-itoken-codeing/RabbitMQ-安装.html#交换机页面)交换机页面

![img](https://funtl.com/assets/Lusifer2018050122030003.png)

### [#](https://funtl.com/zh/spring-cloud-itoken-codeing/RabbitMQ-安装.html#队列页面)队列页面

![img](https://funtl.com/assets/Lusifer2018050122030004.png)

- Name：消息队列的名称，这里是通过程序创建的
- Features：消息队列的类型，durable:true为会持久化消息
- Ready：准备好的消息
- Unacked：未确认的消息
- Total：全部消息
- 备注：如果都为 0 则说明全部消息处理完成

上次更新: 2019-4-19 19:23:15

← [RabbitMQ 简介](https://funtl.com/zh/spring-cloud-itoken-codeing/RabbitMQ-简介.html)[RabbitMQ 使用 ](https://funtl.com/zh/spring-cloud-itoken-codeing/RabbitMQ-使用.html)→