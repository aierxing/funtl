# [#](https://funtl.com/zh/apache-dubbo-rpc/Dubbo-的负载均衡.html#dubbo-的负载均衡)Dubbo 的负载均衡

## [#](https://funtl.com/zh/apache-dubbo-rpc/Dubbo-的负载均衡.html#本节视频)本节视频

- [【视频】Dubbo 实现微服务架构-Dubbo-负载均衡](https://www.bilibili.com/video/av34406643/)

## [#](https://funtl.com/zh/apache-dubbo-rpc/Dubbo-的负载均衡.html#概述)概述

在集群负载均衡时，Dubbo 提供了多种均衡策略，缺省为 `random` 随机调用。

## [#](https://funtl.com/zh/apache-dubbo-rpc/Dubbo-的负载均衡.html#负载均衡策略)负载均衡策略

### [#](https://funtl.com/zh/apache-dubbo-rpc/Dubbo-的负载均衡.html#random-loadbalance)Random LoadBalance

- **随机**，按权重设置随机概率。
- 在一个截面上碰撞的概率高，但调用量越大分布越均匀，而且按概率使用权重后也比较均匀，有利于动态调整提供者权重。

### [#](https://funtl.com/zh/apache-dubbo-rpc/Dubbo-的负载均衡.html#roundrobin-loadbalance)RoundRobin LoadBalance

- **轮询**，按公约后的权重设置轮询比率。
- 存在慢的提供者累积请求的问题，比如：第二台机器很慢，但没挂，当请求调到第二台时就卡在那，久而久之，所有请求都卡在调到第二台上。

### [#](https://funtl.com/zh/apache-dubbo-rpc/Dubbo-的负载均衡.html#leastactive-loadbalance)LeastActive LoadBalance

- **最少活跃调用数**，相同活跃数的随机，活跃数指调用前后计数差。
- 使慢的提供者收到更少请求，因为越慢的提供者的调用前后计数差会越大。

### [#](https://funtl.com/zh/apache-dubbo-rpc/Dubbo-的负载均衡.html#consistenthash-loadbalance)ConsistentHash LoadBalance

- **一致性 Hash**，相同参数的请求总是发到同一提供者。
- 当某一台提供者挂时，原本发往该提供者的请求，基于虚拟节点，平摊到其它提供者，不会引起剧烈变动。
- 算法参见：http://en.wikipedia.org/wiki/Consistent_hashing
- 缺省只对第一个参数 Hash，如果要修改，请配置 ``
- 缺省用 160 份虚拟节点，如果要修改，请配置 ``

## [#](https://funtl.com/zh/apache-dubbo-rpc/Dubbo-的负载均衡.html#配置)配置

### [#](https://funtl.com/zh/apache-dubbo-rpc/Dubbo-的负载均衡.html#服务端服务级别)服务端服务级别

```text
dubbo:
  provider:
    loadbalance: leastactive
```

1
2
3

### [#](https://funtl.com/zh/apache-dubbo-rpc/Dubbo-的负载均衡.html#客户端服务级别)客户端服务级别

```text
dubbo:
  consumer:
    loadbalance: leastactive
```

1
2
3

## [#](https://funtl.com/zh/apache-dubbo-rpc/Dubbo-的负载均衡.html#测试负载均衡)测试负载均衡

### [#](https://funtl.com/zh/apache-dubbo-rpc/Dubbo-的负载均衡.html#修改-userserviceimpl-代码为)修改 `UserServiceImpl` 代码为

```text
package com.funtl.hello.dubbo.service.user.provider.api.impl;

import com.alibaba.dubbo.config.annotation.Service;
import com.funtl.hello.dubbo.service.user.api.UserService;
import org.springframework.beans.factory.annotation.Value;

@Service(version = "${user.service.version}")
public class UserServiceImpl implements UserService {

    @Value("${dubbo.protocol.port}")
    private String port;

    @Override
    public String sayHi() {
        return "Hello Dubbo , i am from port:" + port;
    }
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

注入了端口属性，当消费者访问时可以看出是从哪个端口请求的数据。

### [#](https://funtl.com/zh/apache-dubbo-rpc/Dubbo-的负载均衡.html#修改负载均衡策略为轮询)修改负载均衡策略为轮询

```text
dubbo:
  provider:
    loadbalance: roundrobin
```

1
2
3

### [#](https://funtl.com/zh/apache-dubbo-rpc/Dubbo-的负载均衡.html#测试访问)测试访问

修改端口号并分别启动服务提供者，此时访问服务消费者：http://localhost:9090/hi

浏览器会交替显示：

```text
Hello Dubbo , i am from port:12345
Hello Dubbo , i am from port:12346
```

1
2

## [#](https://funtl.com/zh/apache-dubbo-rpc/Dubbo-的负载均衡.html#附：在-idea-中配置一个工程启动多个实例)附：在 IDEA 中配置一个工程启动多个实例

### [#](https://funtl.com/zh/apache-dubbo-rpc/Dubbo-的负载均衡.html#步骤一)步骤一

点击 `Run -> Edit Configurations...`

![img](https://funtl.com/assets/Lusifer_20181022015716.png)

### [#](https://funtl.com/zh/apache-dubbo-rpc/Dubbo-的负载均衡.html#步骤二)步骤二

选择需要启动多实例的项目并去掉 `Single instance only` 前面的勾

![img](https://funtl.com/assets/Lusifer_20181022015801.png)

### [#](https://funtl.com/zh/apache-dubbo-rpc/Dubbo-的负载均衡.html#步骤三)步骤三

通过修改 `application.yml` 配置文件的 `dubbo.protocol.port` 的端口，启动多个实例，需要多个端口，分别进行启动即可。

上次更新: 2019-4-19 19:23:15

← [第一个 Dubbo 应用程序](https://funtl.com/zh/apache-dubbo-rpc/第一个-Dubbo-应用程序.html)[Dubbo + Kryo 实现高速序列化 ](https://funtl.com/zh/apache-dubbo-rpc/Dubbo-Kryo-实现高速序列化.html)→