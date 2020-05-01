# [#](https://funtl.com/zh/junit/第一个-JUnit-单元测试.html#第一个-junit-单元测试)第一个 JUnit 单元测试

## [#](https://funtl.com/zh/junit/第一个-JUnit-单元测试.html#本节视频)本节视频

- [【视频】基础框架入门-JUnit-第一个 JUnit 单元测试](https://www.bilibili.com/video/av24509731/)

## [#](https://funtl.com/zh/junit/第一个-JUnit-单元测试.html#pom)POM

`pom.xml` 文件如下：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>com.funtl</groupId>
    <artifactId>hello-spring</artifactId>
    <version>1.0.0-SNAPSHOT</version>
    <packaging>jar</packaging>

    <dependencies>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-context</artifactId>
            <version>4.3.17.RELEASE</version>
        </dependency>
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>4.12</version>
            <scope>test</scope>
        </dependency>
    </dependencies>
</project>
```

主要增加了 `junit:junit` 依赖

## [#](https://funtl.com/zh/junit/第一个-JUnit-单元测试.html#创建测试类)创建测试类

在测试包下 `src/main/test` 创建一个名为 `MyTest` 的测试类，代码如下：

```java
package com.funtl.hello.spring.test;

import org.junit.After;
import org.junit.Before;
import org.junit.Test;

public class MyTest {

    /**
     * 执行测试方法前执行
     */
    @Before
    public void before() {
        System.out.println("执行 before() 方法");
    }

    /**
     * 执行测试方法后执行
     */
    @After
    public void after() {
        System.out.println("执行 after() 方法");
    }

    @Test
    public void testSayHi() {
        System.out.println("Hi Log4j");
    }

    @Test
    public void testSayHello() {
        System.out.println("Hello Log4j");
    }
}
```

上次更新: 2018-12-29 1:45:15