# [#](https://funtl.com/zh/spring-mvc/Spring-MVC-ResponseBody-注解.html#spring-mvc-responsebody)Spring MVC @ResponseBody

## [#](https://funtl.com/zh/spring-mvc/Spring-MVC-ResponseBody-注解.html#简介)简介

`@ResponseBody` 注解表示该方法的返回的结果直接写入 HTTP 响应正文（ResponseBody）中，一般在异步获取数据时使用，通常是在使用 `@RequestMapping` 后，返回值通常解析为跳转路径，加上 `@ResponseBody` 后返回结果不会被解析为跳转路径，而是直接写入HTTP 响应正文中。

## [#](https://funtl.com/zh/spring-mvc/Spring-MVC-ResponseBody-注解.html#作用)作用

该注解用于将 `Controller` 的方法返回的对象，通过适当的 `HttpMessageConverter` 转换为指定格式后，写入到 `Response` 对象的 `body` 数据区。

## [#](https://funtl.com/zh/spring-mvc/Spring-MVC-ResponseBody-注解.html#使用时机)使用时机

返回的数据不是 html 标签的页面，而是其他某种格式的数据时（如json、xml等）使用

## [#](https://funtl.com/zh/spring-mvc/Spring-MVC-ResponseBody-注解.html#处理自定义类型)处理自定义类型

如果需要返回自定义对象为 JSON 数据类型，需要增加 `jackson` 依赖，`pom.xml` 配置文件如下：

```html
<!-- Json Begin -->
<dependency>
    <groupId>com.fasterxml.jackson.core</groupId>
    <artifactId>jackson-core</artifactId>
    <version>2.9.5</version>
</dependency>
<dependency>
    <groupId>com.fasterxml.jackson.core</groupId>
    <artifactId>jackson-databind</artifactId>
    <version>2.9.5</version>
</dependency>
<dependency>
    <groupId>com.fasterxml.jackson.core</groupId>
    <artifactId>jackson-annotations</artifactId>
    <version>${jackson.version}</version>
</dependency>
<!-- Json End -->
```

上次更新: 2018-12-29 1:45:15