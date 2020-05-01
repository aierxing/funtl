# [#](https://funtl.com/zh/spring-security-oauth2/PasswordEncoder.html#there-is-no-passwordencoder-mapped)There is no PasswordEncoder mapped

## [#](https://funtl.com/zh/spring-security-oauth2/PasswordEncoder.html#问题描述)问题描述

按照 [基于内存存储令牌](https://funtl.com/zh/spring-security-oauth2/基于内存存储令牌.html) 配置成功后，携授权码使用 POST 请求认证服务器时，服务器返回错误信息

**版本**

- Spring Boot: 2.1.3.RELEASE
- Spring Security: 5.1.4.RELEASE

**日志**

```text
java.lang.IllegalArgumentException: There is no PasswordEncoder mapped for the id "null"
```

1

## [#](https://funtl.com/zh/spring-security-oauth2/PasswordEncoder.html#解决方案)解决方案

Spring Security 5.0 之前版本的 `PasswordEncoder` 接口默认实现为 `NoOpPasswordEncoder` 此时是可以使用明文密码的，在 5.0 之后默认实现类改为 `DelegatingPasswordEncoder` 此时密码必须以加密形式存储。

### [#](https://funtl.com/zh/spring-security-oauth2/PasswordEncoder.html#application-yml)application.yml

删除 `spring.security` 相关配置，修改为

```yaml
spring:
  application:
    name: oauth2-server

server:
  port: 8080
```

1
2
3
4
5
6

### [#](https://funtl.com/zh/spring-security-oauth2/PasswordEncoder.html#websecurityconfiguration)WebSecurityConfiguration

```java
package com.funtl.oauth2.server.config;

import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.security.config.annotation.authentication.builders.AuthenticationManagerBuilder;
import org.springframework.security.config.annotation.method.configuration.EnableGlobalMethodSecurity;
import org.springframework.security.config.annotation.web.configuration.EnableWebSecurity;
import org.springframework.security.config.annotation.web.configuration.WebSecurityConfigurerAdapter;
import org.springframework.security.crypto.bcrypt.BCryptPasswordEncoder;

@Configuration
@EnableWebSecurity
@EnableGlobalMethodSecurity(prePostEnabled = true, securedEnabled = true, jsr250Enabled = true)
public class WebSecurityConfiguration extends WebSecurityConfigurerAdapter {

    @Bean
    public BCryptPasswordEncoder passwordEncoder() {
        // 设置默认的加密方式
        return new BCryptPasswordEncoder();
    }

    @Override
    protected void configure(AuthenticationManagerBuilder auth) throws Exception {

        auth.inMemoryAuthentication()
                // 在内存中创建用户并为密码加密
                .withUser("user").password(passwordEncoder().encode("123456")).roles("USER")
                .and()
                .withUser("admin").password(passwordEncoder().encode("123456")).roles("ADMIN");

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
19
20
21
22
23
24
25
26
27
28
29
30
31
32

### [#](https://funtl.com/zh/spring-security-oauth2/PasswordEncoder.html#authorizationserverconfiguration)AuthorizationServerConfiguration

```java
package com.funtl.oauth2.server.config;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.context.annotation.Configuration;
import org.springframework.security.crypto.bcrypt.BCryptPasswordEncoder;
import org.springframework.security.oauth2.config.annotation.configurers.ClientDetailsServiceConfigurer;
import org.springframework.security.oauth2.config.annotation.web.configuration.AuthorizationServerConfigurerAdapter;
import org.springframework.security.oauth2.config.annotation.web.configuration.EnableAuthorizationServer;

@Configuration
@EnableAuthorizationServer
public class AuthorizationServerConfiguration extends AuthorizationServerConfigurerAdapter {

    // 注入 WebSecurityConfiguration 中配置的 BCryptPasswordEncoder
    @Autowired
    private BCryptPasswordEncoder passwordEncoder;

    @Override
    public void configure(ClientDetailsServiceConfigurer clients) throws Exception {
        clients
                .inMemory()
                .withClient("client")
                // 还需要为 secret 加密
                .secret(passwordEncoder.encode("secret"))
                .authorizedGrantTypes("authorization_code")
                .scopes("app")
                .redirectUris("http://www.funtl.com");

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
19
20
21
22
23
24
25
26
27
28
29
30

## [#](https://funtl.com/zh/spring-security-oauth2/PasswordEncoder.html#测试访问)测试访问

通过 CURL 或是 Postman 请求

```bash
curl -X POST -H "Content-Type: application/x-www-form-urlencoded" -d 'grant_type=authorization_code&code=1JuO6V' "http://client:secret@localhost:8080/oauth/token"
```

1

![img](https://funtl.com/assets1/Lusifer_20190402232952.png)

得到响应结果如下：

```json
{
    "access_token": "016d8d4a-dd6e-4493-b590-5f072923c413",
    "token_type": "bearer",
    "expires_in": 43199,
    "scope": "app"
}
```

1
2
3
4
5
6

上次更新: 2019-4-3 1:48:25

← [基于 RBAC 的自定义认证](https://funtl.com/zh/spring-security-oauth2/基于-RBAC-的自定义认证.html)[对认证服务器的修改 ](https://funtl.com/zh/spring-security-oauth2/对认证服务器的修改.html)→