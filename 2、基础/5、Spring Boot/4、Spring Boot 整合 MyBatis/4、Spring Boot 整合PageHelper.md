# [#](https://funtl.com/zh/spring-boot-mybatis/Spring-Boot-整合-PageHelper.html#spring-boot-整合-pagehelper)Spring Boot 整合 PageHelper

## [#](https://funtl.com/zh/spring-boot-mybatis/Spring-Boot-整合-PageHelper.html#概述)概述

PageHelper 是 Mybatis 的分页插件，支持多数据库、多数据源。可以简化数据库的分页查询操作，整合过程也极其简单，只需引入依赖即可。

## [#](https://funtl.com/zh/spring-boot-mybatis/Spring-Boot-整合-PageHelper.html#引入依赖)引入依赖

在 `pom.xml` 文件中引入 `pagehelper-spring-boot-starter` 依赖

```text
<dependency>
    <groupId>com.github.pagehelper</groupId>
    <artifactId>pagehelper-spring-boot-starter</artifactId>
    <version>1.2.5</version>
</dependency>
```

1
2
3
4
5

**PS：** 具体使用方法在 `测试 MyBatis 操作数据库` 章节中进行介绍，本章节仅为准备环节。

上次更新: 2018-12-31 18:51:48

← [Spring Boot 整合 tk.mybatis](https://funtl.com/zh/spring-boot-mybatis/Spring-Boot-整合-tk-mybatis.html)[使用 MyBatis 的 Maven 插件生成代码 ](https://funtl.com/zh/spring-boot-mybatis/使用-MyBatis-的-Maven-插件生成代码.html)→