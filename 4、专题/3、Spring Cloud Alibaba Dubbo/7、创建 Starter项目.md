# [#](https://funtl.com/zh/spring-cloud-alibaba-dubbo-vue/创建-Starter-项目.html#创建-starter-项目)创建 Starter 项目

## [#](https://funtl.com/zh/spring-cloud-alibaba-dubbo-vue/创建-Starter-项目.html#pom)POM

创建一个名为 `spring-cloud-alibaba-dubbo-starter` 的模块，其父工程为我们的工程项目 `spring-cloud-alibaba-dubbo-parent`；改模块就是利用 Spring Boot Starter 扩展机制实现的自定义 Starter 项目了；

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <parent>
        <groupId>com.funtl</groupId>
        <artifactId>spring-cloud-alibaba-dubbo-parent</artifactId>
        <version>1.0.0-SNAPSHOT</version>
    </parent>

    <artifactId>spring-cloud-alibaba-dubbo-starter</artifactId>
    <url>http://www.funtl.com</url>

    <dependencies>
        <!-- Spring -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-openfeign</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-actuator</artifactId>
        </dependency>

        <!-- Projects -->
        <dependency>
            <groupId>com.funtl</groupId>
            <artifactId>spring-cloud-alibaba-dubbo-core</artifactId>
            <version>${project.parent.version}</version>
        </dependency>
        <dependency>
            <groupId>com.funtl</groupId>
            <artifactId>spring-cloud-alibaba-dubbo-nacos</artifactId>
            <version>${project.parent.version}</version>
        </dependency>
    </dependencies>

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

上次更新: 2019-3-16 10:19:40

← [创建注册中心管理模块](https://funtl.com/zh/spring-cloud-alibaba-dubbo-vue/创建注册中心管理模块.html)[创建服务提供者 ](https://funtl.com/zh/spring-cloud-alibaba-dubbo-vue/创建服务提供者.html)→