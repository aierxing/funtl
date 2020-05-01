# [#](https://funtl.com/zh/spring-boot/#spring-简史)Spring 简史

## [#](https://funtl.com/zh/spring-boot/#本节视频)本节视频

- [【视频】微服务框架-SpringBoot-简史](https://www.bilibili.com/video/av27784037)

## [#](https://funtl.com/zh/spring-boot/#spring-1-x-时代)Spring 1.x 时代

在 Spring1.x 时代，都是通过 xml 文件配置 bean，随着项目的不断扩大，需要将 xml 配置分放到不同的配置文件中，需要频繁的在 java 类和 xml 配置文件中切换。

## [#](https://funtl.com/zh/spring-boot/#spring-2-x-时代)Spring 2.x 时代

随着 JDK 1.5 带来的注解支持，Spring2.x 可以使用注解对 Bean 进行申明和注入，大大的减少了 xml 配置文件，同时也大大简化了项目的开发。

那么，问题来了，究竟是应该使用 xml 还是注解呢？

最佳实践：

- 应用的基本配置用 xml，比如：数据源、资源文件等
- 业务开发用注解，比如：Service 中注入 bean 等

## [#](https://funtl.com/zh/spring-boot/#spring-3-x-时代)Spring 3.x 时代

从 Spring3.x 开始提供了 Java 配置方式，使用 Java 配置方式可以更好的理解你配置的 Bean，现在我们就处于这个时代，并且 Spring4.x 和 Spring boot 都推荐使用 java 配置的方式。

## [#](https://funtl.com/zh/spring-boot/#spring-5-x-时代)Spring 5.x 时代

Spring5.x 是 Java 界首个支持响应式的 Web 框架，是 Spring 的一个重要版本，距离 Spring4.x 差不多四年。在此期间，大多数增强都是在 SpringBoot 项目中完成的，其最大的亮点就是提供了完整的端到端响应式编程的支持（新增 Spring WebFlux 模块）。

Spring WebFlux 同时支持使用旧的 Spring MVC 注解声明 `Reactive Controller`。和传统的 `MVC Controller` 不同，`Reactive Controller` 操作的是 **非阻塞** 的 `ServerHttpRequest` 和 `ServerHttpResponse`，而不再是 Spring MVC 里的 HttpServletRequest 和 HttpServletResponse。

至此也代表着 Java 正式迎来了响应式异步编程的时代。

上次更新: 2018-12-31 18:51:48