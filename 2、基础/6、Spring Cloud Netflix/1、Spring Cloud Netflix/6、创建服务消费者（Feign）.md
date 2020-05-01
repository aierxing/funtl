# [#](https://funtl.com/zh/spring-cloud-netflix/Spring-Cloud-创建服务消费者（Feign）.html#创建服务消费者（feign）)创建服务消费者（Feign）

## [#](https://funtl.com/zh/spring-cloud-netflix/Spring-Cloud-创建服务消费者（Feign）.html#本节视频)本节视频

- [【视频】微服务框架-SpringCloud-创建服务消费者（Feign）](https://www.bilibili.com/video/av27864637)

## [#](https://funtl.com/zh/spring-cloud-netflix/Spring-Cloud-创建服务消费者（Feign）.html#概述)概述

Feign 是一个声明式的伪 Http 客户端，它使得写 Http 客户端变得更简单。使用 Feign，只需要创建一个接口并注解。它具有可插拔的注解特性，可使用 Feign 注解和 JAX-RS 注解。Feign 支持可插拔的编码器和解码器。Feign 默认集成了 Ribbon，并和 Eureka 结合，默认实现了负载均衡的效果

- Feign 采用的是基于接口的注解
- Feign 整合了 ribbon

## [#](https://funtl.com/zh/spring-cloud-netflix/Spring-Cloud-创建服务消费者（Feign）.html#创建服务消费者)创建服务消费者

创建一个工程名为 `hello-spring-cloud-web-admin-feign` 的服务消费者项目，`pom.xml` 配置如下：

```text
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <parent>
        <groupId>com.funtl</groupId>
        <artifactId>hello-spring-cloud-dependencies</artifactId>
        <version>1.0.0-SNAPSHOT</version>
        <relativePath>../hello-spring-cloud-dependencies/pom.xml</relativePath>
    </parent>

    <artifactId>hello-spring-cloud-web-admin-feign</artifactId>
    <packaging>jar</packaging>

    <name>hello-spring-cloud-web-admin-feign</name>
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
            <artifactId>spring-boot-starter-tomcat</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-thymeleaf</artifactId>
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
            <artifactId>spring-cloud-starter-netflix-eureka-server</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-openfeign</artifactId>
        </dependency>
        <!-- Spring Cloud End -->

        <!-- 解决 thymeleaf 模板引擎一定要执行严格的 html5 格式校验问题 -->
        <dependency>
            <groupId>net.sourceforge.nekohtml</groupId>
            <artifactId>nekohtml</artifactId>
        </dependency>
    </dependencies>

    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
                <configuration>
                    <mainClass>com.funtl.hello.spring.cloud.web.admin.feign.WebAdminFeignApplication</mainClass>
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
61
62
63
64
65
66
67
68
69
70
71
72
73
74

主要是增加了 Feign 的依赖

```text
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-openfeign</artifactId>
</dependency>
```

1
2
3
4

### [#](https://funtl.com/zh/spring-cloud-netflix/Spring-Cloud-创建服务消费者（Feign）.html#application)Application

通过 `@EnableFeignClients` 注解开启 Feign 功能

```text
package com.funtl.hello.spring.cloud.web.admin.feign;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.client.discovery.EnableDiscoveryClient;
import org.springframework.cloud.openfeign.EnableFeignClients;

@SpringBootApplication
@EnableDiscoveryClient
@EnableFeignClients
public class WebAdminFeignApplication {
    public static void main(String[] args) {
        SpringApplication.run(WebAdminFeignApplication.class, args);
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

### [#](https://funtl.com/zh/spring-cloud-netflix/Spring-Cloud-创建服务消费者（Feign）.html#application-yml)application.yml

设置程序端口号为：`8765`

```text
spring:
  application:
    name: hello-spring-cloud-web-admin-feign
  thymeleaf:
    cache: false
    mode: LEGACYHTML5
    encoding: UTF-8
    servlet:
      content-type: text/html

server:
  port: 8765

eureka:
  client:
    serviceUrl:
      defaultZone: http://localhost:8761/eureka/
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

### [#](https://funtl.com/zh/spring-cloud-netflix/Spring-Cloud-创建服务消费者（Feign）.html#创建-feign-接口)创建 Feign 接口

通过 `@FeignClient("服务名")` 注解来指定调用哪个服务。代码如下：

```text
package com.funtl.hello.spring.cloud.web.admin.feign.service;

import org.springframework.cloud.openfeign.FeignClient;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestMethod;
import org.springframework.web.bind.annotation.RequestParam;

@FeignClient(value = "hello-spring-cloud-service-admin")
public interface AdminService {

    @RequestMapping(value = "hi", method = RequestMethod.GET)
    public String sayHi(@RequestParam(value = "message") String message);
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

### [#](https://funtl.com/zh/spring-cloud-netflix/Spring-Cloud-创建服务消费者（Feign）.html#创建测试用的-controller)创建测试用的 Controller

```text
package com.funtl.hello.spring.cloud.web.admin.feign.controller;

import com.funtl.hello.spring.cloud.web.admin.feign.service.AdminService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestMethod;
import org.springframework.web.bind.annotation.RequestParam;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class AdminController {

    @Autowired
    private AdminService adminService;

    @RequestMapping(value = "hi", method = RequestMethod.GET)
    public String sayHi(@RequestParam String message) {
        return adminService.sayHi(message);
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

### [#](https://funtl.com/zh/spring-cloud-netflix/Spring-Cloud-创建服务消费者（Feign）.html#测试访问)测试访问

在浏览器上多次访问 http://localhost:8765/hi?message=HelloFeign

浏览器交替显示：

```text
Hi，your message is :"HelloFeign" i am from port：8762
Hi，your message is :"HelloFeign" i am from port：8763
```

1
2

请求成功则表示我们已经成功实现了 Feign 功能来访问不同端口的实例

上次更新: 2018-12-31 18:51:48

← [创建服务消费者（Ribbon）](https://funtl.com/zh/spring-cloud-netflix/Spring-Cloud-创建服务消费者（Ribbon）.html)[使用熔断器防止服务雪崩 ](https://funtl.com/zh/spring-cloud-netflix/Spring-Cloud-使用熔断器防止服务雪崩.html)→