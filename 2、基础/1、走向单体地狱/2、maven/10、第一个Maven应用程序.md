# [#](https://funtl.com/zh/maven/第一个-Maven-应用程序.html#第一个-maven-应用程序)第一个 Maven 应用程序

## [#](https://funtl.com/zh/maven/第一个-Maven-应用程序.html#本节视频)本节视频

- [【视频】使用 Maven 构建应用-第一个 Maven 应用程序](https://www.bilibili.com/video/av24455466/)

## [#](https://funtl.com/zh/maven/第一个-Maven-应用程序.html#概述)概述

下面我们来学习如何使用 Maven 创建一个 Java Web 应用程序

## [#](https://funtl.com/zh/maven/第一个-Maven-应用程序.html#创建-maven-项目)创建 Maven 项目

选择 `File` -> `New` -> `Project...`

![img](https://funtl.com/assets/Lusifer1528025821.png)

选择 `Maven` 项目

![img](https://funtl.com/assets/Lusifer1528025904.png)

填写项目信息

![img](https://funtl.com/assets/Lusifer1528025957.png)

选择工作空间

![img](https://funtl.com/assets/Lusifer1528026009.png)

## [#](https://funtl.com/zh/maven/第一个-Maven-应用程序.html#目录结构)目录结构

Java Web 的 Maven 基本结构如下：

```text
├─src
│  ├─main
│  │  ├─java
│  │  ├─resources
│  │  └─webapp
│  │      └─WEB-INF
│  └─test
│      └─java
```

结构说明：

- ```
  src
  ```

  ：源码目录

  - `src/main/java`：Java 源码目录
  - `src/main/resources`：资源文件目录
  - `src/main/webapp`：Web 相关目录
  - `src/test`：单元测试

## [#](https://funtl.com/zh/maven/第一个-Maven-应用程序.html#idea-maven-项目管理)IDEA Maven 项目管理

在 IDEA 界面的右侧 `Maven Projects` 选项，可以管理 Maven 项目的整个生命周期、插件、依赖等

![img](https://funtl.com/assets/Lusifer1528027638.png)

## [#](https://funtl.com/zh/maven/第一个-Maven-应用程序.html#完善-java-web-程序)完善 Java Web 程序

### [#](https://funtl.com/zh/maven/第一个-Maven-应用程序.html#pom)POM

修改 `pom.xml` 配置，内容如下：

```text
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>com.funtl</groupId>
    <artifactId>hello-maven</artifactId>
    <version>1.0.0-SNAPSHOT</version>
    <packaging>war</packaging>

    <dependencies>
        <dependency>
            <groupId>javax.servlet</groupId>
            <artifactId>javax.servlet-api</artifactId>
            <version>4.0.1</version>
            <scope>provided</scope>
        </dependency>

        <dependency>
            <groupId>org.slf4j</groupId>
            <artifactId>log4j-over-slf4j</artifactId>
            <version>1.7.25</version>
        </dependency>
    </dependencies>
</project>
```

配置说明：

- `packaging`：打包方式，这里是 `war` 包，表示为 Java Web 应用程序
- `dependencies`：项目依赖配置，整个项目生命周期中所需的依赖都在这里配置

### [#](https://funtl.com/zh/maven/第一个-Maven-应用程序.html#创建测试用-servlet)创建测试用 Servlet

创建一个 `Servlet` 用于测试请求

```java
package com.funtl.hello.maven.servlet;

import javax.servlet.ServletException;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;

public class HelloServlet extends HttpServlet {

    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        req.getRequestDispatcher("/index.jsp").forward(req, resp);
    }

    @Override
    protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        super.doPost(req, resp);
    }
}
```

### [#](https://funtl.com/zh/maven/第一个-Maven-应用程序.html#创建测试用-jsp)创建测试用 JSP

创建一个 `JSP` 页面，用于测试请求

```jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>Title</title>
</head>
<body>
    Hello Maven
</body>
</html>
```

### [#](https://funtl.com/zh/maven/第一个-Maven-应用程序.html#创建-log4j-的配置文件)创建 Log4J 的配置文件

在 `src/main/resources` 目录下创建 `log4j.properties` 配置文件，内容如下：

```properties
log4j.rootLogger=INFO, console, file

log4j.appender.console=org.apache.log4j.ConsoleAppender
log4j.appender.console.layout=org.apache.log4j.PatternLayout
log4j.appender.console.layout.ConversionPattern=%d %p [%c] - %m%n

log4j.appender.file=org.apache.log4j.DailyRollingFileAppender
log4j.appender.file.File=logs/log.log
log4j.appender.file.layout=org.apache.log4j.PatternLayout
log4j.appender.A3.MaxFileSize=1024KB
log4j.appender.A3.MaxBackupIndex=10
log4j.appender.file.layout.ConversionPattern=%d %p [%c] - %m%n
```

### [#](https://funtl.com/zh/maven/第一个-Maven-应用程序.html#配置-web-xml)配置 `web.xml`

`web.xml` 配置文件如下：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
         version="4.0">

    <servlet>
        <servlet-name>HelloServlet</servlet-name>
        <servlet-class>com.funtl.hello.maven.servlet.HelloServlet</servlet-class>
    </servlet>
    <servlet-mapping>
        <servlet-name>HelloServlet</servlet-name>
        <url-pattern>/servlet/hello</url-pattern>
    </servlet-mapping>
</web-app>
```

### [#](https://funtl.com/zh/maven/第一个-Maven-应用程序.html#测试运行)测试运行

按照之前章节 `第一个 IDEA 应用程序` 配置完 `Tomcat` 后直接运行，打开浏览器访问 http://localhost:8080 显示如下：

```text
Hello Maven
```

上次更新: 2019-4-19 19:23:15