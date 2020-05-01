#  Spring 整合 Web

## [#](https://funtl.com/zh/spring-web/#本节视频)本节视频

- [【视频】Spring Web 与 Bean 装配-Spring 整合 Web](https://www.bilibili.com/video/av24698265/)

## [#](https://funtl.com/zh/spring-web/#容器初始化)容器初始化

启动容器时需要自动装载 `ApplicationContext`，Spring 提供的 `ContextLoaderListener` 就是为了自动装配 `ApplicationContext` 的配置信息

### [#](https://funtl.com/zh/spring-web/#pom)POM

需要在 `pom.xml` 增加 `org.springframework:spring-web` 依赖

```xml
<dependency>
    <groupId>javax.servlet</groupId>
    <artifactId>javax.servlet-api</artifactId>
    <version>3.1.0</version>
    <scope>provided</scope>
</dependency>

<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-web</artifactId>
    <version>4.3.17.RELEASE</version>
</dependency>
```

### [#](https://funtl.com/zh/spring-web/#配置-web-xml)配置 `web.xml`

`web.xml` 配置如下

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_3_1.xsd"
         version="3.1">

    <context-param>
        <param-name>contextConfigLocation</param-name>
        <param-value>classpath:spring-context*.xml</param-value>
    </context-param>
    <listener>
        <listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
    </listener>
</web-app>
```

上次更新: 2018-12-29 1:45:15