# [#](https://funtl.com/zh/spring-cloud-netflix/Spring-Boot-Admin-服务端.html#spring-boot-admin-服务端)Spring Boot Admin 服务端

## [#](https://funtl.com/zh/spring-cloud-netflix/Spring-Boot-Admin-服务端.html#本节视频)本节视频

- [【视频】微服务框架-SpringCloud-服务监控-服务端](https://www.bilibili.com/video/av28096101/)

## [#](https://funtl.com/zh/spring-cloud-netflix/Spring-Boot-Admin-服务端.html#创建-spring-boot-admin-server)创建 Spring Boot Admin Server

创建一个工程名为 `hello-spring-cloud-admin` 的项目，`pom.xml` 文件如下：

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

    <artifactId>hello-spring-cloud-admin</artifactId>
    <packaging>jar</packaging>

    <name>hello-spring-cloud-admin</name>
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
            <artifactId>spring-boot-starter-webflux</artifactId>
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

        <dependency>
            <groupId>org.jolokia</groupId>
            <artifactId>jolokia-core</artifactId>
        </dependency>
        <dependency>
            <groupId>de.codecentric</groupId>
            <artifactId>spring-boot-admin-starter-server</artifactId>
        </dependency>
        <!-- Spring Boot End -->

        <!-- Spring Cloud Begin -->
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-zipkin</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-netflix-eureka-server</artifactId>
        </dependency>
        <!-- Spring Cloud End -->
    </dependencies>

    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
                <configuration>
                    <mainClass>com.funtl.hello.spring.cloud.admin.AdminApplication</mainClass>
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
75
76
77

主要增加了 2 个依赖，`org.jolokia:jolokia-core`、`de.codecentric:spring-boot-admin-starter-server`

```text
<dependency>
    <groupId>org.jolokia</groupId>
    <artifactId>jolokia-core</artifactId>
</dependency>
<dependency>
    <groupId>de.codecentric</groupId>
    <artifactId>spring-boot-admin-starter-server</artifactId>
</dependency>
```

1
2
3
4
5
6
7
8

其中 `spring-boot-admin-starter-server` 的版本号为：`2.0.0`，这里没写版本号是因为我已将版本号托管到 `dependencies` 项目中

## [#](https://funtl.com/zh/spring-cloud-netflix/Spring-Boot-Admin-服务端.html#application)Application

通过 `@EnableAdminServer` 注解开启 Admin 功能

```text
package com.funtl.hello.spring.cloud.admin;

import de.codecentric.boot.admin.server.config.EnableAdminServer;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.netflix.eureka.EnableEurekaClient;

@SpringBootApplication
@EnableEurekaClient
@EnableAdminServer
public class AdminApplication {
    public static void main(String[] args) {
        SpringApplication.run(AdminApplication.class, args);
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

## [#](https://funtl.com/zh/spring-cloud-netflix/Spring-Boot-Admin-服务端.html#application-yml)application.yml

设置端口号为：`8084`

```text
spring:
  application:
    name: hello-spring-cloud-admin
  zipkin:
    base-url: http://localhost:9411

server:
  port: 8084

management:
  endpoint:
    health:
      show-details: always
  endpoints:
    web:
      exposure:
        # 注意：此处在视频里是 include: ["health", "info"] 但已无效了，请修改
        include: health,info

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
18
19
20
21
22
23

主要增加了 Spring Boot Admin Server 的相关配置

```text
management:
  endpoint:
    health:
      show-details: always
  endpoints:
    web:
      exposure:
        # 注意：此处在视频里是 include: ["health", "info"] 但已无效了，请修改
        include: health,info
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

## [#](https://funtl.com/zh/spring-cloud-netflix/Spring-Boot-Admin-服务端.html#测试访问监控中心)测试访问监控中心

打开浏览器访问：http://localhost:8084 会出现以下界面

![img](https://funtl.com/assets/Lusifer2018060105410004.png)

上次更新: 2019-4-19 19:23:15

← [Spring Boot Admin](https://funtl.com/zh/spring-cloud-netflix/Spring-Boot-Admin-服务监控.html)[Spring Boot Admin 客户端 ](https://funtl.com/zh/spring-cloud-netflix/Spring-Boot-Admin-客户端.html)→