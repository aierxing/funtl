# [#](https://funtl.com/zh/spring-boot-thymeleaf/为什么使用-Thymeleaf.html#为什么使用-thymeleaf)为什么使用 Thymeleaf

## [#](https://funtl.com/zh/spring-boot-thymeleaf/为什么使用-Thymeleaf.html#本节视频)本节视频

- [【视频】微服务框架-SpringBoot-Thymeleaf-为什么使用模板引擎](https://www.bilibili.com/video/av27784242)

## [#](https://funtl.com/zh/spring-boot-thymeleaf/为什么使用-Thymeleaf.html#概述)概述

如果希望以 Jar 形式发布模块则尽量不要使用 JSP 相关知识，这是**因为 JSP 在内嵌的 Servlet 容器上运行有一些问题 (内嵌 Tomcat、 Jetty 不支持 Jar 形式运行 JSP**，Undertow 不支持 JSP)。

Spring Boot 中推荐使用 Thymeleaf 作为模板引擎，因为 Thymeleaf 提供了完美的 Spring MVC 支持

Spring Boot 提供了大量模板引擎，包括：

- FreeMarker
- Groovy
- Mustache
- **Thymeleaf**
- Velocity
- **Beetl**

上次更新: 2018-12-31 18:51:48

← 