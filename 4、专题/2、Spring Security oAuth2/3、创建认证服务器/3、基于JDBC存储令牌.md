# [#](https://funtl.com/zh/spring-security-oauth2/基于-JDBC-存储令牌.html#基于-jdbc-存储令牌)基于 JDBC 存储令牌

## [#](https://funtl.com/zh/spring-security-oauth2/基于-JDBC-存储令牌.html#本节视频)本节视频

- [Spring Security oAuth2-基于 JDBC 存储令牌](https://www.bilibili.com/video/av48590637/?p=10)

## [#](https://funtl.com/zh/spring-security-oauth2/基于-JDBC-存储令牌.html#概述)概述

本章节 **基于 JDBC 存储令牌** 的模式用于演示最基本的操作，帮助大家快速理解 oAuth2 认证服务器中 "认证"、"授权"、"访问令牌” 的基本概念

**操作流程**

![img](https://funtl.com/assets1/Lusifer_201904030001.png)

- 初始化 oAuth2 相关表

- 在数据库中配置客户端

- 配置认证服务器

  - 配置数据源：`DataSource`

  - 配置令牌存储方式：`TokenStore` -> `JdbcTokenStore`

  - 配置客户端读取方式：`ClientDetailsService` -> `JdbcClientDetailsService`

  - 配置服务端点信息：

    ```
    AuthorizationServerEndpointsConfigurer
    ```

    - `tokenStore`：设置令牌存储方式

  - 配置客户端信息：

    ```
    ClientDetailsServiceConfigurer
    ```

    - `withClientDetails`：设置客户端配置读取方式

- 配置 Web 安全

  - 配置密码加密方式：`BCryptPasswordEncoder`
  - 配置认证信息：`AuthenticationManagerBuilder`

- 通过 GET 请求访问认证服务器获取授权码

  - 端点：`/oauth/authorize`

- 通过 POST 请求利用授权码访问认证服务器获取令牌

  - 端点：`/oauth/token`

**附：默认的端点 URL**

- `/oauth/authorize`：授权端点
- `/oauth/token`：令牌端点
- `/oauth/confirm_access`：用户确认授权提交端点
- `/oauth/error`：授权服务错误信息端点
- `/oauth/check_token`：用于资源服务访问的令牌解析端点
- `/oauth/token_key`：提供公有密匙的端点，如果你使用 JWT 令牌的话

## [#](https://funtl.com/zh/spring-security-oauth2/基于-JDBC-存储令牌.html#初始化-oauth2-相关表)初始化 oAuth2 相关表

使用官方提供的建表脚本初始化 oAuth2 相关表，地址如下：

```text
https://github.com/spring-projects/spring-security-oauth/blob/master/spring-security-oauth2/src/test/resources/schema.sql
```

1

由于我们使用的是 MySQL 数据库，默认建表语句中主键为 `VARCHAR(256)`，这超过了最大的主键长度，请手动修改为 `128`，并用 `BLOB` 替换语句中的 `LONGVARBINARY` 类型，修改后的建表脚本如下：

```sql
CREATE TABLE `clientdetails` (
  `appId` varchar(128) NOT NULL,
  `resourceIds` varchar(256) DEFAULT NULL,
  `appSecret` varchar(256) DEFAULT NULL,
  `scope` varchar(256) DEFAULT NULL,
  `grantTypes` varchar(256) DEFAULT NULL,
  `redirectUrl` varchar(256) DEFAULT NULL,
  `authorities` varchar(256) DEFAULT NULL,
  `access_token_validity` int(11) DEFAULT NULL,
  `refresh_token_validity` int(11) DEFAULT NULL,
  `additionalInformation` varchar(4096) DEFAULT NULL,
  `autoApproveScopes` varchar(256) DEFAULT NULL,
  PRIMARY KEY (`appId`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8;

CREATE TABLE `oauth_access_token` (
  `token_id` varchar(256) DEFAULT NULL,
  `token` blob,
  `authentication_id` varchar(128) NOT NULL,
  `user_name` varchar(256) DEFAULT NULL,
  `client_id` varchar(256) DEFAULT NULL,
  `authentication` blob,
  `refresh_token` varchar(256) DEFAULT NULL,
  PRIMARY KEY (`authentication_id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8;

CREATE TABLE `oauth_approvals` (
  `userId` varchar(256) DEFAULT NULL,
  `clientId` varchar(256) DEFAULT NULL,
  `scope` varchar(256) DEFAULT NULL,
  `status` varchar(10) DEFAULT NULL,
  `expiresAt` timestamp NULL DEFAULT NULL,
  `lastModifiedAt` timestamp NULL DEFAULT NULL
) ENGINE=InnoDB DEFAULT CHARSET=utf8;

CREATE TABLE `oauth_client_details` (
  `client_id` varchar(128) NOT NULL,
  `resource_ids` varchar(256) DEFAULT NULL,
  `client_secret` varchar(256) DEFAULT NULL,
  `scope` varchar(256) DEFAULT NULL,
  `authorized_grant_types` varchar(256) DEFAULT NULL,
  `web_server_redirect_uri` varchar(256) DEFAULT NULL,
  `authorities` varchar(256) DEFAULT NULL,
  `access_token_validity` int(11) DEFAULT NULL,
  `refresh_token_validity` int(11) DEFAULT NULL,
  `additional_information` varchar(4096) DEFAULT NULL,
  `autoapprove` varchar(256) DEFAULT NULL,
  PRIMARY KEY (`client_id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8;

CREATE TABLE `oauth_client_token` (
  `token_id` varchar(256) DEFAULT NULL,
  `token` blob,
  `authentication_id` varchar(128) NOT NULL,
  `user_name` varchar(256) DEFAULT NULL,
  `client_id` varchar(256) DEFAULT NULL,
  PRIMARY KEY (`authentication_id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8;

CREATE TABLE `oauth_code` (
  `code` varchar(256) DEFAULT NULL,
  `authentication` blob
) ENGINE=InnoDB DEFAULT CHARSET=utf8;

CREATE TABLE `oauth_refresh_token` (
  `token_id` varchar(256) DEFAULT NULL,
  `token` blob,
  `authentication` blob
) ENGINE=InnoDB DEFAULT CHARSET=utf8;
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
33
34
35
36
37
38
39
40
41
42
43
44
45
46
47
48
49
50
51
52
53
54
55
56
57
58
59
60
61
62
63
64
65
66
67
68
69

## [#](https://funtl.com/zh/spring-security-oauth2/基于-JDBC-存储令牌.html#在数据库中配置客户端)在数据库中配置客户端

在表 `oauth_client_details` 中增加一条客户端配置记录，需要设置的字段如下：

- `client_id`：客户端标识
- `client_secret`：客户端安全码，**此处不能是明文，需要加密**
- `scope`：客户端授权范围
- `authorized_grant_types`：客户端授权类型
- `web_server_redirect_uri`：服务器回调地址

使用 `BCryptPasswordEncoder` 为客户端安全码加密，代码如下：

```java
System.out.println(new BCryptPasswordEncoder().encode("secret"));
```

1

数据库配置客户端效果图如下：

![img](https://funtl.com/assets1/Lusifer_20190403150110.png)

## [#](https://funtl.com/zh/spring-security-oauth2/基于-JDBC-存储令牌.html#pom)POM

由于使用了 JDBC 存储，我们需要增加相关依赖，数据库连接池部分弃用 `Druid` 改为 `HikariCP` （**号称全球最快连接池**）

```xml
<dependency>
    <groupId>com.zaxxer</groupId>
    <artifactId>HikariCP</artifactId>
    <version>${hikaricp.version}</version>
</dependency>
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-jdbc</artifactId>
    <exclusions>
        <!-- 排除 tomcat-jdbc 以使用 HikariCP -->
        <exclusion>
            <groupId>org.apache.tomcat</groupId>
            <artifactId>tomcat-jdbc</artifactId>
        </exclusion>
    </exclusions>
</dependency>
<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
    <version>${mysql.version}</version>
</dependency>
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

## [#](https://funtl.com/zh/spring-security-oauth2/基于-JDBC-存储令牌.html#配置认证服务器)配置认证服务器

创建一个类继承 `AuthorizationServerConfigurerAdapter` 并添加相关注解：

- `@Configuration`
- `@EnableAuthorizationServer`

```java
package com.funtl.oauth2.server.config;

import org.springframework.boot.context.properties.ConfigurationProperties;
import org.springframework.boot.jdbc.DataSourceBuilder;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.context.annotation.Primary;
import org.springframework.security.oauth2.config.annotation.configurers.ClientDetailsServiceConfigurer;
import org.springframework.security.oauth2.config.annotation.web.configuration.AuthorizationServerConfigurerAdapter;
import org.springframework.security.oauth2.config.annotation.web.configuration.EnableAuthorizationServer;
import org.springframework.security.oauth2.config.annotation.web.configurers.AuthorizationServerEndpointsConfigurer;
import org.springframework.security.oauth2.provider.ClientDetailsService;
import org.springframework.security.oauth2.provider.client.JdbcClientDetailsService;
import org.springframework.security.oauth2.provider.token.TokenStore;
import org.springframework.security.oauth2.provider.token.store.JdbcTokenStore;

import javax.sql.DataSource;

@Configuration
@EnableAuthorizationServer
public class AuthorizationServerConfiguration extends AuthorizationServerConfigurerAdapter {

    @Bean
    @Primary
    @ConfigurationProperties(prefix = "spring.datasource")
    public DataSource dataSource() {
        // 配置数据源（注意，我使用的是 HikariCP 连接池），以上注解是指定数据源，否则会有冲突
        return DataSourceBuilder.create().build();
    }

    @Bean
    public TokenStore tokenStore() {
        // 基于 JDBC 实现，令牌保存到数据
        return new JdbcTokenStore(dataSource());
    }

    @Bean
    public ClientDetailsService jdbcClientDetails() {
        // 基于 JDBC 实现，需要事先在数据库配置客户端信息
        return new JdbcClientDetailsService(dataSource());
    }

    @Override
    public void configure(AuthorizationServerEndpointsConfigurer endpoints) throws Exception {
        // 设置令牌
        endpoints.tokenStore(tokenStore());
    }

    @Override
    public void configure(ClientDetailsServiceConfigurer clients) throws Exception {
        // 读取客户端配置
        clients.withClientDetails(jdbcClientDetails());
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
33
34
35
36
37
38
39
40
41
42
43
44
45
46
47
48
49
50
51
52
53
54

## [#](https://funtl.com/zh/spring-security-oauth2/基于-JDBC-存储令牌.html#服务器安全配置)服务器安全配置

创建一个类继承 `WebSecurityConfigurerAdapter` 并添加相关注解：

- `@Configuration`
- `@EnableWebSecurity`
- `@EnableGlobalMethodSecurity(prePostEnabled = true, securedEnabled = true, jsr250Enabled = true)`：全局方法拦截

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

## [#](https://funtl.com/zh/spring-security-oauth2/基于-JDBC-存储令牌.html#application-yml)application.yml

```yaml
spring:
  application:
    name: oauth2-server
  datasource:
    type: com.zaxxer.hikari.HikariDataSource
    driver-class-name: com.mysql.cj.jdbc.Driver
    jdbc-url: jdbc:mysql://192.168.141.128:3307/oauth2?useUnicode=true&characterEncoding=utf-8&useSSL=false
    username: root
    password: 123456
    hikari:
      minimum-idle: 5
      idle-timeout: 600000
      maximum-pool-size: 10
      auto-commit: true
      pool-name: MyHikariCP
      max-lifetime: 1800000
      connection-timeout: 30000
      connection-test-query: SELECT 1

server:
  port: 8080
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

## [#](https://funtl.com/zh/spring-security-oauth2/基于-JDBC-存储令牌.html#访问获取授权码)访问获取授权码

打开浏览器，输入地址：

```text
http://localhost:8080/oauth/authorize?client_id=client&response_type=code
```

1

第一次访问会跳转到登录页面

![img](https://funtl.com/assets1/Lusifer_20190401195014.png)

验证成功后会询问用户是否授权客户端

![img](https://funtl.com/assets1/Lusifer_20190401195129.png)

选择授权后会跳转到我的博客，浏览器地址上还会包含一个授权码（`code=1JuO6V`），浏览器地址栏会显示如下地址：

```text
http://www.funtl.com/?code=1JuO6V
```

1

有了这个授权码就可以获取访问令牌了

## [#](https://funtl.com/zh/spring-security-oauth2/基于-JDBC-存储令牌.html#通过授权码向服务器申请令牌)通过授权码向服务器申请令牌

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

操作成功后数据库 `oauth_access_token` 表中会增加一笔记录，效果图如下：

![img](https://funtl.com/assets1/Lusifer_20190403150529.png)

上次更新: 2019-4-20 19:07:04

← [基于内存存储令牌](https://funtl.com/zh/spring-security-oauth2/基于内存存储令牌.html)[RBAC 基于角色的权限控制 ](https://funtl.com/zh/spring-security-oauth2/RBAC-基于角色的权限控制.html)→