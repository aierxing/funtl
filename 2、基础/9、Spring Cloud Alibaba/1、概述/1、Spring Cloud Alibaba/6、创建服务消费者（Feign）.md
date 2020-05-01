# [#](https://funtl.com/zh/spring-cloud-alibaba/创建服务消费者-Feign.html#创建服务消费者（feign）)创建服务消费者（Feign）

## [#](https://funtl.com/zh/spring-cloud-alibaba/创建服务消费者-Feign.html#本节视频)本节视频

- [Spring Cloud Alibaba-Nacos-服务消费者(Feign)](https://www.bilibili.com/video/av40521126/)

## [#](https://funtl.com/zh/spring-cloud-alibaba/创建服务消费者-Feign.html#概述)概述

Feign 是一个声明式的伪 Http 客户端，它使得写 Http 客户端变得更简单。使用 Feign，只需要创建一个接口并注解。它具有可插拔的注解特性，可使用 Feign 注解和 JAX-RS 注解。Feign 支持可插拔的编码器和解码器。Feign 默认集成了 Ribbon，Nacos 也很好的兼容了 Feign，默认实现了负载均衡的效果

- Feign 采用的是基于接口的注解
- Feign 整合了 ribbon

## [#](https://funtl.com/zh/spring-cloud-alibaba/创建服务消费者-Feign.html#pom)POM

创建一个工程名为 `hello-spring-cloud-alibaba-nacos-consumer-feign` 的服务消费者项目，`pom.xml` 配置如下：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <parent>
        <groupId>com.funtl</groupId>
        <artifactId>hello-spring-cloud-alibaba-dependencies</artifactId>
        <version>1.0.0-SNAPSHOT</version>
        <relativePath>../hello-spring-cloud-alibaba-dependencies/pom.xml</relativePath>
    </parent>

    <artifactId>hello-spring-cloud-alibaba-nacos-consumer-feign</artifactId>
    <packaging>jar</packaging>

    <name>hello-spring-cloud-alibaba-nacos-consumer-feign</name>
    <url>http://www.funtl.com</url>
    <inceptionYear>2018-Now</inceptionYear>

    <dependencies>
        <!-- Spring Boot Begin -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-actuator</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
        <!-- Spring Boot End -->

        <!-- Spring Cloud Begin -->
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-openfeign</artifactId>
        </dependency>
        <!-- Spring Cloud End -->
    </dependencies>

    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
                <configuration>
                    <mainClass>com.funtl.hello.spring.cloud.alibaba.nacos.consumer.feign.NacosConsumerFeignApplication</mainClass>
                </configuration>
            </plugin>
        </plugins>
    </build>
</project>
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

主要增加了 `org.springframework.cloud:spring-cloud-starter-openfeign` 依赖

## [#](https://funtl.com/zh/spring-cloud-alibaba/创建服务消费者-Feign.html#application)Application

通过 `@EnableFeignClients` 注解开启 Feign 功能

```java
package com.funtl.hello.spring.cloud.alibaba.nacos.consumer.feign;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.client.discovery.EnableDiscoveryClient;
import org.springframework.cloud.openfeign.EnableFeignClients;

@SpringBootApplication
@EnableDiscoveryClient
@EnableFeignClients
public class NacosConsumerFeignApplication {
    public static void main(String[] args) {
        SpringApplication.run(NacosConsumerFeignApplication.class, args);
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

## [#](https://funtl.com/zh/spring-cloud-alibaba/创建服务消费者-Feign.html#创建-feign-接口)创建 Feign 接口

通过 `@FeignClient("服务名")` 注解来指定调用哪个服务。代码如下：

```java
package com.funtl.hello.spring.cloud.alibaba.nacos.consumer.feign.service;

import org.springframework.cloud.openfeign.FeignClient;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PathVariable;

@FeignClient(value = "nacos-provider")
public interface EchoService {

    @GetMapping(value = "/echo/{message}")
    String echo(@PathVariable("message") String message);
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

## [#](https://funtl.com/zh/spring-cloud-alibaba/创建服务消费者-Feign.html#controller)Controller

```java
package com.funtl.hello.spring.cloud.alibaba.nacos.consumer.feign.controller;

import com.funtl.hello.spring.cloud.alibaba.nacos.consumer.feign.service.EchoService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class NacosConsumerFeignController {

    @Autowired
    private EchoService echoService;

    @GetMapping(value = "/echo/hi")
    public String echo() {
        return echoService.echo("Hi Feign");
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

## [#](https://funtl.com/zh/spring-cloud-alibaba/创建服务消费者-Feign.html#application-yml)application.yml

```yaml
spring:
  application:
    name: nacos-consumer-feign
  cloud:
    nacos:
      discovery:
        server-addr: 127.0.0.1:8848

server:
  port: 9092

management:
  endpoints:
    web:
      exposure:
        include: "*"
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

## [#](https://funtl.com/zh/spring-cloud-alibaba/创建服务消费者-Feign.html#启动工程)启动工程

通过浏览器访问 `http://localhost:8848/nacos`，即 Nacos Server 网址

![img](https://funtl.com/assets1/Lusifer_20190106143035.png)

你会发现多了一个名为 `nacos-consumer-feign` 的服务

这时打开 `http://localhost:9092/echo/hi` ，你会在浏览器上看到：

```html
Hello Nacos Discovery Hi Feign
```

1

## [#](https://funtl.com/zh/spring-cloud-alibaba/创建服务消费者-Feign.html#测试负载均衡)测试负载均衡

- 启动多个 `consumer-provider` 实例，效果图如下：

![img](https://funtl.com/assets1/Lusifer_20190106144323.png)

- 修改 `consumer-provider` 项目中的 `Controller` 代码，用于确定负载均衡生效

```java
package com.funtl.hello.spring.cloud.alibaba.nacos.provider;

import org.springframework.beans.factory.annotation.Value;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.client.discovery.EnableDiscoveryClient;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.RestController;

@SpringBootApplication
@EnableDiscoveryClient
public class NacosProviderApplication {
    public static void main(String[] args) {
        SpringApplication.run(NacosProviderApplication.class, args);
    }

    @Value("${server.port}")
    private String port;

    @RestController
    public class EchoController {
        @GetMapping(value = "/echo/{message}")
        public String echo(@PathVariable String message) {
            return "Hello Nacos Discovery " + message + " i am from port " + port;
        }
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
25
26
27
28

- 在浏览器上多次访问 `http://localhost:9092/echo/hi` ，浏览器交替显示：

```html
Hello Nacos Discovery Hi Feign i am from port 8081
Hello Nacos Discovery Hi Feign i am from port 8082
```

1
2

上次更新: 2019-1-12 10:58:33

← [创建服务消费者](https://funtl.com/zh/spring-cloud-alibaba/创建服务消费者.html)[使用熔断器防止服务雪崩 ](https://funtl.com/zh/spring-cloud-alibaba/使用熔断器防止服务雪崩.html)→