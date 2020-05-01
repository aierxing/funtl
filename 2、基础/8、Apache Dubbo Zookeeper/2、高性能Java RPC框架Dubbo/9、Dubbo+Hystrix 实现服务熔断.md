# [#](https://funtl.com/zh/apache-dubbo-rpc/Dubbo-Hystrix-实现服务熔断.html#dubbo-hystrix-实现服务熔断)Dubbo + Hystrix 实现服务熔断

## [#](https://funtl.com/zh/apache-dubbo-rpc/Dubbo-Hystrix-实现服务熔断.html#本节视频)本节视频

- [【视频】Dubbo 实现微服务架构-Dubbo-使用 Hystrix 实现服务熔断](https://www.bilibili.com/video/av34446940/)

## [#](https://funtl.com/zh/apache-dubbo-rpc/Dubbo-Hystrix-实现服务熔断.html#熔断器简介)熔断器简介

在微服务架构中，根据业务来拆分成一个个的服务，服务与服务之间可以通过 `RPC` 相互调用。为了保证其高可用，单个服务通常会集群部署。由于网络原因或者自身的原因，服务并不能保证 100% 可用，如果单个服务出现问题，调用这个服务就会出现线程阻塞，此时若有大量的请求涌入，`Servlet` 容器的线程资源会被消耗完毕，导致服务瘫痪。服务与服务之间的依赖性，故障会传播，会对整个微服务系统造成灾难性的严重后果，这就是服务故障的 **“雪崩”** 效应。

为了解决这个问题，业界提出了熔断器模型。

Netflix 开源了 Hystrix 组件，实现了熔断器模式，Spring Cloud 对这一组件进行了整合。在微服务架构中，一个请求需要调用多个服务是非常常见的，如下图：

![img](https://funtl.com/assets/Lusifer201805292246007.png)

较底层的服务如果出现故障，会导致连锁故障。当对特定的服务的调用的不可用达到一个阀值（Hystrix 是 **5 秒 20 次**） 熔断器将会被打开。

![img](https://funtl.com/assets/Lusifer201805292246008.png)

熔断器打开后，为了避免连锁故障，通过 `fallback` 方法可以直接返回一个固定值。

## [#](https://funtl.com/zh/apache-dubbo-rpc/Dubbo-Hystrix-实现服务熔断.html#dubbo-provider-中使用熔断器)Dubbo Provider 中使用熔断器

### [#](https://funtl.com/zh/apache-dubbo-rpc/Dubbo-Hystrix-实现服务熔断.html#在-pom-xml-中增加依赖)在 `pom.xml` 中增加依赖

```text
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-netflix-hystrix</artifactId>
    <version>2.0.1.RELEASE</version>
</dependency>
```

1
2
3
4
5

### [#](https://funtl.com/zh/apache-dubbo-rpc/Dubbo-Hystrix-实现服务熔断.html#在-application-中增加-enablehystrix-注解)在 Application 中增加 `@EnableHystrix` 注解

```text
package com.funtl.hello.dubbo.service.user.provider;

import com.alibaba.dubbo.container.Main;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.netflix.hystrix.EnableHystrix;

@EnableHystrix
@SpringBootApplication
public class HelloDubboServiceUserProviderApplication {
    public static void main(String[] args) {
        SpringApplication.run(HelloDubboServiceUserProviderApplication.class, args);
        Main.main(args);
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

### [#](https://funtl.com/zh/apache-dubbo-rpc/Dubbo-Hystrix-实现服务熔断.html#在-service-中增加-hystrixcommand-注解)在 Service 中增加 `@HystrixCommand` 注解

在调用方法上增加 `@HystrixCommand` 配置，此时调用会经过 Hystrix 代理

```text
package com.funtl.hello.dubbo.service.user.provider.api.impl;

import com.alibaba.dubbo.config.annotation.Service;
import com.funtl.hello.dubbo.service.user.api.UserService;
import com.netflix.hystrix.contrib.javanica.annotation.HystrixCommand;
import com.netflix.hystrix.contrib.javanica.annotation.HystrixProperty;
import org.springframework.beans.factory.annotation.Value;

@Service(version = "${user.service.version}")
public class UserServiceImpl implements UserService {

    @Value("${dubbo.protocol.port}")
    private String port;

    @HystrixCommand(commandProperties = {
            @HystrixProperty(name = "circuitBreaker.requestVolumeThreshold", value = "10"),
            @HystrixProperty(name = "execution.isolation.thread.timeoutInMilliseconds", value = "2000")
    })
    @Override
    public String sayHi() {
//        return "Hello Dubbo, i am from port:" + port;
        throw new RuntimeException("Exception to show hystrix enabled.");
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
18
19
20
21
22
23
24

### [#](https://funtl.com/zh/apache-dubbo-rpc/Dubbo-Hystrix-实现服务熔断.html#测试熔断器)测试熔断器

此时我们再次请求服务提供者，浏览器会报 500 异常

```text
Exception to show hystrix enabled.
```

1

## [#](https://funtl.com/zh/apache-dubbo-rpc/Dubbo-Hystrix-实现服务熔断.html#dubbo-consumer-中使用熔断器)Dubbo Consumer 中使用熔断器

### [#](https://funtl.com/zh/apache-dubbo-rpc/Dubbo-Hystrix-实现服务熔断.html#在-pom-xml-中增加依赖-2)在 `pom.xml` 中增加依赖

```text
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-netflix-hystrix</artifactId>
    <version>2.0.1.RELEASE</version>
</dependency>
```

1
2
3
4
5

### [#](https://funtl.com/zh/apache-dubbo-rpc/Dubbo-Hystrix-实现服务熔断.html#在-application-中增加-enablehystrix-注解-2)在 Application 中增加 `@EnableHystrix` 注解

```text
package com.funtl.hello.dubbo.service.user.consumer;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.netflix.hystrix.EnableHystrix;

@EnableHystrix
@SpringBootApplication
public class HelloDubboServiceUserConsumerApplication {
    public static void main(String[] args) {
        SpringApplication.run(HelloDubboServiceUserConsumerApplication.class, args);
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

### [#](https://funtl.com/zh/apache-dubbo-rpc/Dubbo-Hystrix-实现服务熔断.html#在调用方法上增加-hystrixcommand-注解，并指定-fallbackmethod-方法)在调用方法上增加 `@HystrixCommand` 注解，并指定 `fallbackMethod` 方法

```text
package com.funtl.hello.dubbo.service.user.consumer.controller;

import com.alibaba.dubbo.config.annotation.Reference;
import com.funtl.hello.dubbo.service.user.api.UserService;
import com.netflix.hystrix.contrib.javanica.annotation.HystrixCommand;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class UserController {

    @Reference(version = "${user.service.version}")
    private UserService userService;

    @HystrixCommand(fallbackMethod = "hiError")
    @RequestMapping(value = "hi")
    public String sayHi() {
        return userService.sayHi();
    }

    public String hiError() {
        return "Hystrix fallback";
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
18
19
20
21
22
23
24

### [#](https://funtl.com/zh/apache-dubbo-rpc/Dubbo-Hystrix-实现服务熔断.html#测试熔断器-2)测试熔断器

此时我们再次请求服务提供者，浏览器会显示：

```text
Hystrix fallback
```

1

至此，Dubbo + Hystrix 配置完成

上次更新: 2019-4-19 19:23:15

← [Dubbo + Kryo 实现高速序列化](https://funtl.com/zh/apache-dubbo-rpc/Dubbo-Kryo-实现高速序列化.html)[Dubbo + Hystrix 熔断器仪表盘 ](https://funtl.com/zh/apache-dubbo-rpc/Dubbo-Hystrix-熔断器仪表盘.html)→