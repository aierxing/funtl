# [#](https://funtl.com/zh/supplement2/Spring-Boot-配置-CORS.html#spring-boot-配置-cors)Spring Boot 配置 CORS

## [#](https://funtl.com/zh/supplement2/Spring-Boot-配置-CORS.html#使用-java-配置的方式)使用 Java 配置的方式

```text
/**
 * 跨域配置
 * <p>Title: CorsConfiguration</p>
 * <p>Description: </p>
 *
 * @author Lusifer
 * @version 1.0.0
 * @date 2018/3/8 22:56
 */
@Configuration
public class CORSConfiguration extends WebMvcConfigurerAdapter {
    @Override
    public void addCorsMappings(CorsRegistry registry) {
        registry.addMapping("/**").allowedOrigins("*")
                .allowedMethods("GET", "HEAD", "POST", "PUT", "DELETE", "OPTIONS")
                .allowCredentials(false).maxAge(3600);
    }
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
18

## [#](https://funtl.com/zh/supplement2/Spring-Boot-配置-CORS.html#使用注解的方式)使用注解的方式

```text
@CrossOrigin(origins = "*", maxAge = 3600)
```

1

## [#](https://funtl.com/zh/supplement2/Spring-Boot-配置-CORS.html#解决跨域后的效果图)解决跨域后的效果图

![img](https://funtl.com/assets/Lusifer1520521282.png)

上次更新: 2019-4-19 19:23:15

← [什么是跨域问题](https://funtl.com/zh/supplement2/什么是跨域问题.html)[Fiddler 简介 ](https://funtl.com/zh/supplement2/Fiddler-简介.html)→