# [#](https://funtl.com/zh/spring-cloud-itoken-codeing/Spring-Boot-拦截器.html#spring-boot-配置拦截器)Spring Boot 配置拦截器

## [#](https://funtl.com/zh/spring-cloud-itoken-codeing/Spring-Boot-拦截器.html#定义拦截器)定义拦截器

```text
public class MyInterceptor implements HandlerInterceptor {
    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) {
        return false;
    }

    @Override
    public void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler, ModelAndView modelAndView) {

    }

    @Override
    public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex) {

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

## [#](https://funtl.com/zh/spring-cloud-itoken-codeing/Spring-Boot-拦截器.html#配置拦截器)配置拦截器

```text
@Configuration
public class InterceptorConfig implements WebMvcConfigurer {

    @Override
    public void addInterceptors(InterceptorRegistry registry) {
        registry.addInterceptor(new MyInterceptor()).addPathPatterns("/**");
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

上次更新: 2018-12-31 18:51:48

← [实战单点登录](https://funtl.com/zh/spring-cloud-itoken-codeing/实战单点登录.html)[重构改善既有代码的设计 ](https://funtl.com/zh/spring-cloud-itoken-codeing/重构改善既有代码的设计.html)→