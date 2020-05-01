# [#](https://funtl.com/zh/spring-boot-mybatis/Spring-Boot-整合-tk-mybatis.html#spring-boot-整合-tk-mybatis)Spring Boot 整合 tk.mybatis

## [#](https://funtl.com/zh/spring-boot-mybatis/Spring-Boot-整合-tk-mybatis.html#概述)概述

tk.mybatis 是在 MyBatis 框架的基础上提供了很多工具，让开发更加高效

## [#](https://funtl.com/zh/spring-boot-mybatis/Spring-Boot-整合-tk-mybatis.html#引入依赖)引入依赖

在 `pom.xml` 文件中引入 `mapper-spring-boot-starter` 依赖，该依赖会自动引入 MyBaits 相关依赖

```text
<dependency>
    <groupId>tk.mybatis</groupId>
    <artifactId>mapper-spring-boot-starter</artifactId>
    <version>2.0.2</version>
</dependency>
```

1
2
3
4
5

## [#](https://funtl.com/zh/spring-boot-mybatis/Spring-Boot-整合-tk-mybatis.html#配置-application-yml)配置 `application.yml`

配置 MyBatis

```text
mybatis:
    type-aliases-package: 实体类的存放路径，如：com.funtl.hello.spring.boot.entity
    mapper-locations: classpath:mapper/*.xml
```

1
2
3

## [#](https://funtl.com/zh/spring-boot-mybatis/Spring-Boot-整合-tk-mybatis.html#创建一个通用的父级接口)创建一个通用的父级接口

主要作用是让 DAO 层的接口继承该接口，以达到使用 tk.mybatis 的目的

```text
package com.funtl.utils;

import tk.mybatis.mapper.common.Mapper;
import tk.mybatis.mapper.common.MySqlMapper;

/**
 * 自己的 Mapper
 * 特别注意，该接口不能被扫描到，否则会出错
 * <p>Title: MyMapper</p>
 * <p>Description: </p>
 *
 * @author Lusifer
 * @version 1.0.0
 * @date 2018/5/29 0:57
 */
public interface MyMapper<T> extends Mapper<T>, MySqlMapper<T> {
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

**PS：** 具体使用方法在 `测试 MyBatis 操作数据库` 章节中进行介绍，本章节仅为准备环节。

上次更新: 2018-12-31 18:51:48

← [Spring Boot 整合 Druid](https://funtl.com/zh/spring-boot-mybatis/Spring-Boot-整合-Druid.html)[Spring Boot 整合 PageHelper ](https://funtl.com/zh/spring-boot-mybatis/Spring-Boot-整合-PageHelper.html)→