# [#](https://funtl.com/zh/spring-cloud-netflix/Spring-Boot-Admin-客户端.html#spring-boot-admin-客户端)Spring Boot Admin 客户端

## [#](https://funtl.com/zh/spring-cloud-netflix/Spring-Boot-Admin-客户端.html#本节视频)本节视频

- [【视频】微服务框架-SpringCloud-服务监控-客户端](https://www.bilibili.com/video/av28096126/)

## [#](https://funtl.com/zh/spring-cloud-netflix/Spring-Boot-Admin-客户端.html#创建-spring-boot-admin-client)创建 Spring Boot Admin Client

创建一个工程名为 `hello-spring-cloud-admin-client` 的项目，`pom.xml` 文件如下：

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

    <artifactId>hello-spring-cloud-admin-client</artifactId>
    <packaging>jar</packaging>

    <name>hello-spring-cloud-admin-client</name>
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
            <artifactId>spring-boot-admin-starter-client</artifactId>
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
                    <mainClass>com.funtl.hello.spring.cloud.admin.client.AdminClientApplication</mainClass>
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

主要增加了 2 个依赖，`org.jolokia:jolokia-core`、`de.codecentric:spring-boot-admin-starter-client`

```text
<dependency>
    <groupId>org.jolokia</groupId>
    <artifactId>jolokia-core</artifactId>
</dependency>
<dependency>
    <groupId>de.codecentric</groupId>
    <artifactId>spring-boot-admin-starter-client</artifactId>
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

其中 `spring-boot-admin-starter-client` 的版本号为：`2.0.0`，这里没写版本号是因为我已将版本号托管到 `dependencies` 项目中

## [#](https://funtl.com/zh/spring-cloud-netflix/Spring-Boot-Admin-客户端.html#application)Application

程序入口类没有特别需要修改的地方

```text
package com.funtl.hello.spring.cloud.admin.client;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.client.discovery.EnableDiscoveryClient;

@SpringBootApplication
@EnableDiscoveryClient
public class AdminClientApplication {
    public static void main(String[] args) {
        SpringApplication.run(AdminClientApplication.class, args);
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

## [#](https://funtl.com/zh/spring-cloud-netflix/Spring-Boot-Admin-客户端.html#application-yml)application.yml

设置端口号为：`8085`，并设置 Spring Boot Admin 的服务端地址

```text
spring:
  application:
    name: hello-spring-cloud-admin-client
  boot:
    admin:
      client:
        url: http://localhost:8084
  zipkin:
    base-url: http://localhost:9411

server:
  port: 8085

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

主要增加了 Spring Boot Admin Client 相关配置

```text
spring:
  boot:
    admin:
      client:
        url: http://localhost:8084
```

1
2
3
4
5

## [#](https://funtl.com/zh/spring-cloud-netflix/Spring-Boot-Admin-客户端.html#测试服务监控)测试服务监控

依次启动两个应用，打开浏览器访问：http://localhost:8084 界面显示如下

![img](https://funtl.com/assets/Lusifer2018060105410005.png)

从图中可以看到，我们的 Admin Client 已经上线了，至此说明监控中心搭建成功

### [#](https://funtl.com/zh/spring-cloud-netflix/Spring-Boot-Admin-客户端.html#wallboard)WallBoard

![img](https://funtl.com/assets/Lusifer2018060105410006.png)

### [#](https://funtl.com/zh/spring-cloud-netflix/Spring-Boot-Admin-客户端.html#journal)Journal

![img](https://funtl.com/assets/Lusifer2018060105410007.png)

上次更新: 2019-4-19 19:23:15

← [Spring Boot Admin 服务端](https://funtl.com/zh/spring-cloud-netflix/Spring-Boot-Admin-服务端.html)