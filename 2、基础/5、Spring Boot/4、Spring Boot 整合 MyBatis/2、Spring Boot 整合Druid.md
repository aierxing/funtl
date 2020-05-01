# [#](https://funtl.com/zh/spring-boot-mybatis/Spring-Boot-整合-Druid.html#spring-boot-整合-druid)Spring Boot 整合 Druid

## [#](https://funtl.com/zh/spring-boot-mybatis/Spring-Boot-整合-Druid.html#概述)概述

Druid 是阿里巴巴开源平台上的一个项目，整个项目由数据库连接池、插件框架和 SQL 解析器组成。该项目主要是为了扩展 JDBC 的一些限制，可以让程序员实现一些特殊的需求，比如向密钥服务请求凭证、统计 SQL 信息、SQL 性能收集、SQL 注入检查、SQL 翻译等，程序员可以通过定制来实现自己需要的功能。

Druid 是目前最好的数据库连接池，在功能、性能、扩展性方面，都超过其他数据库连接池，包括 DBCP、C3P0、BoneCP、Proxool、JBoss DataSource。Druid 已经在阿里巴巴部署了超过 600 个应用，经过多年生产环境大规模部署的严苛考验。Druid 是阿里巴巴开发的号称为监控而生的数据库连接池！

## [#](https://funtl.com/zh/spring-boot-mybatis/Spring-Boot-整合-Druid.html#引入依赖)引入依赖

在 `pom.xml` 文件中引入 `druid-spring-boot-starter` 依赖

```text
<dependency>
    <groupId>com.alibaba</groupId>
    <artifactId>druid-spring-boot-starter</artifactId>
    <version>1.1.10</version>
</dependency>
```

1
2
3
4
5

引入数据库连接依赖

```text
<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
    <scope>runtime</scope>
</dependency>
```

1
2
3
4
5

## [#](https://funtl.com/zh/spring-boot-mybatis/Spring-Boot-整合-Druid.html#配置-application-yml)配置 `application.yml`

在 `application.yml` 中配置数据库连接

```text
spring:
  datasource:
    druid:
      url: jdbc:mysql://ip:port/dbname?useUnicode=true&characterEncoding=utf-8&useSSL=false
      username: root
      password: 123456
      initial-size: 1
      min-idle: 1
      max-active: 20
      test-on-borrow: true
      # MySQL 8.x: com.mysql.cj.jdbc.Driver
      driver-class-name: com.mysql.jdbc.Driver
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

**PS：** 具体使用方法在 `测试 MyBatis 操作数据库` 章节中进行介绍，本章节仅为准备环节。

上次更新: 2018-12-31 18:51:48

← [本节视频](https://funtl.com/zh/spring-boot-mybatis/)[Spring Boot 整合 tk.mybatis ](https://funtl.com/zh/spring-boot-mybatis/Spring-Boot-整合-tk-mybatis.html)→