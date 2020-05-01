# [#](https://funtl.com/zh/spring-security-oauth2/创建-oAuth2-案例模块.html#创建-oauth2-案例模块)创建 oAuth2 案例模块

## [#](https://funtl.com/zh/spring-security-oauth2/创建-oAuth2-案例模块.html#pom)POM

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <parent>
        <groupId>com.funtl</groupId>
        <artifactId>spring-boot-samples</artifactId>
        <version>1.0.0-SNAPSHOT</version>
    </parent>

    <artifactId>spring-security-oauth2</artifactId>
    <packaging>pom</packaging>
    <url>http://www.funtl.com</url>

    <modules>
        <!-- 工程模块请随着项目的不断完善自行添加 -->
    </modules>

    <licenses>
        <license>
            <name>Apache 2.0</name>
            <url>https://www.apache.org/licenses/LICENSE-2.0.txt</url>
        </license>
    </licenses>

    <developers>
        <developer>
            <id>liwemin</id>
            <name>Lusifer Lee</name>
            <email>lee.lusifer@gmail.com</email>
        </developer>
    </developers>

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

上次更新: 2019-4-1 19:55:19

← [创建统一的依赖管理模块](https://funtl.com/zh/spring-security-oauth2/创建统一的依赖管理模块.html)[创建认证服务器模块 ](https://funtl.com/zh/spring-security-oauth2/创建认证服务器模块.html)→