# [#](https://funtl.com/zh/spring-security-oauth2/基于-RBAC-的自定义认证.html#基于-rbac-的自定义认证)基于 RBAC 的自定义认证

## [#](https://funtl.com/zh/spring-security-oauth2/基于-RBAC-的自定义认证.html#本节视频)本节视频

- [Spring Security oAuth2-基于 RBAC 的自定义认证1](https://www.bilibili.com/video/av48590637/?p=13)
- [Spring Security oAuth2-基于 RBAC 的自定义认证2](https://www.bilibili.com/video/av48590637/?p=14)

## [#](https://funtl.com/zh/spring-security-oauth2/基于-RBAC-的自定义认证.html#概述)概述

在实际开发中，我们的用户信息都是存在数据库里的，本章节基于 [RBAC 模型](https://funtl.com/zh/spring-security-oauth2/RBAC-基于角色的权限控制.html) 将用户的认证信息与数据库对接，实现真正的用户认证与授权

**操作流程**

继续 [基于 JDBC 存储令牌](https://funtl.com/zh/spring-security-oauth2/基于-JDBC-存储令牌.html) 章节的代码开发

- 初始化 RBAC 相关表
- 在数据库中配置“用户”、“角色”、“权限”相关信息
- 数据库操作使用 `tk.mybatis` 框架，故需要增加相关依赖
- 配置 Web 安全
  - 配置使用自定义认证与授权
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

## [#](https://funtl.com/zh/spring-security-oauth2/基于-RBAC-的自定义认证.html#初始化-rbac-相关表)初始化 RBAC 相关表

```sql
CREATE TABLE `tb_permission` (
  `id` bigint(20) NOT NULL AUTO_INCREMENT,
  `parent_id` bigint(20) DEFAULT NULL COMMENT '父权限',
  `name` varchar(64) NOT NULL COMMENT '权限名称',
  `enname` varchar(64) NOT NULL COMMENT '权限英文名称',
  `url` varchar(255) NOT NULL COMMENT '授权路径',
  `description` varchar(200) DEFAULT NULL COMMENT '备注',
  `created` datetime NOT NULL,
  `updated` datetime NOT NULL,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB AUTO_INCREMENT=44 DEFAULT CHARSET=utf8 COMMENT='权限表';
insert  into `tb_permission`(`id`,`parent_id`,`name`,`enname`,`url`,`description`,`created`,`updated`) values 
(37,0,'系统管理','System','/',NULL,'2019-04-04 23:22:54','2019-04-04 23:22:56'),
(38,37,'用户管理','SystemUser','/users/',NULL,'2019-04-04 23:25:31','2019-04-04 23:25:33'),
(39,38,'查看用户','SystemUserView','',NULL,'2019-04-04 15:30:30','2019-04-04 15:30:43'),
(40,38,'新增用户','SystemUserInsert','',NULL,'2019-04-04 15:30:31','2019-04-04 15:30:44'),
(41,38,'编辑用户','SystemUserUpdate','',NULL,'2019-04-04 15:30:32','2019-04-04 15:30:45'),
(42,38,'删除用户','SystemUserDelete','',NULL,'2019-04-04 15:30:48','2019-04-04 15:30:45');

CREATE TABLE `tb_role` (
  `id` bigint(20) NOT NULL AUTO_INCREMENT,
  `parent_id` bigint(20) DEFAULT NULL COMMENT '父角色',
  `name` varchar(64) NOT NULL COMMENT '角色名称',
  `enname` varchar(64) NOT NULL COMMENT '角色英文名称',
  `description` varchar(200) DEFAULT NULL COMMENT '备注',
  `created` datetime NOT NULL,
  `updated` datetime NOT NULL,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB AUTO_INCREMENT=38 DEFAULT CHARSET=utf8 COMMENT='角色表';
insert  into `tb_role`(`id`,`parent_id`,`name`,`enname`,`description`,`created`,`updated`) values 
(37,0,'超级管理员','admin',NULL,'2019-04-04 23:22:03','2019-04-04 23:22:05');

CREATE TABLE `tb_role_permission` (
  `id` bigint(20) NOT NULL AUTO_INCREMENT,
  `role_id` bigint(20) NOT NULL COMMENT '角色 ID',
  `permission_id` bigint(20) NOT NULL COMMENT '权限 ID',
  PRIMARY KEY (`id`)
) ENGINE=InnoDB AUTO_INCREMENT=43 DEFAULT CHARSET=utf8 COMMENT='角色权限表';
insert  into `tb_role_permission`(`id`,`role_id`,`permission_id`) values 
(37,37,37),
(38,37,38),
(39,37,39),
(40,37,40),
(41,37,41),
(42,37,42);

CREATE TABLE `tb_user` (
  `id` bigint(20) NOT NULL AUTO_INCREMENT,
  `username` varchar(50) NOT NULL COMMENT '用户名',
  `password` varchar(64) NOT NULL COMMENT '密码，加密存储',
  `phone` varchar(20) DEFAULT NULL COMMENT '注册手机号',
  `email` varchar(50) DEFAULT NULL COMMENT '注册邮箱',
  `created` datetime NOT NULL,
  `updated` datetime NOT NULL,
  PRIMARY KEY (`id`),
  UNIQUE KEY `username` (`username`) USING BTREE,
  UNIQUE KEY `phone` (`phone`) USING BTREE,
  UNIQUE KEY `email` (`email`) USING BTREE
) ENGINE=InnoDB AUTO_INCREMENT=38 DEFAULT CHARSET=utf8 COMMENT='用户表';
insert  into `tb_user`(`id`,`username`,`password`,`phone`,`email`,`created`,`updated`) values 
(37,'admin','$2a$10$9ZhDOBp.sRKat4l14ygu/.LscxrMUcDAfeVOEPiYwbcRkoB09gCmi','15888888888','lee.lusifer@gmail.com','2019-04-04 23:21:27','2019-04-04 23:21:29');

CREATE TABLE `tb_user_role` (
  `id` bigint(20) NOT NULL AUTO_INCREMENT,
  `user_id` bigint(20) NOT NULL COMMENT '用户 ID',
  `role_id` bigint(20) NOT NULL COMMENT '角色 ID',
  PRIMARY KEY (`id`)
) ENGINE=InnoDB AUTO_INCREMENT=38 DEFAULT CHARSET=utf8 COMMENT='用户角色表';
insert  into `tb_user_role`(`id`,`user_id`,`role_id`) values 
(37,37,37);
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
70

由于使用了 `BCryptPasswordEncoder` 的加密方式，故用户密码需要加密，代码如下：

```java
System.out.println(new BCryptPasswordEncoder().encode("123456"));
```

1

## [#](https://funtl.com/zh/spring-security-oauth2/基于-RBAC-的自定义认证.html#pom)POM

数据库操作采用 `tk.mybatis` 框架，需增加相关依赖

```xml
<dependency>
    <groupId>tk.mybatis</groupId>
    <artifactId>mapper-spring-boot-starter</artifactId>
</dependency>
```

1
2
3
4

## [#](https://funtl.com/zh/spring-security-oauth2/基于-RBAC-的自定义认证.html#关键步骤)关键步骤

由于本次增加了 MyBatis 相关操作，代码增加较多，可以参考我 [**GitHub**](https://github.com/topsale/spring-boot-samples/tree/master/spring-security-oauth2) 上的源码，下面仅列出关键步骤及代码

### [#](https://funtl.com/zh/spring-security-oauth2/基于-RBAC-的自定义认证.html#获取用户信息)获取用户信息

目的是为了实现自定义认证授权时可以通过数据库查询用户信息，Spring Security oAuth2 要求使用 `username` 的方式查询，提供相关用户信息后，认证工作由框架自行完成

```java
package com.funtl.oauth2.server.service.impl;

import com.funtl.oauth2.server.domain.TbUser;
import com.funtl.oauth2.server.mapper.TbUserMapper;
import com.funtl.oauth2.server.service.TbUserService;
import org.springframework.stereotype.Service;
import tk.mybatis.mapper.entity.Example;

import javax.annotation.Resource;

@Service
public class TbUserServiceImpl implements TbUserService {

    @Resource
    private TbUserMapper tbUserMapper;

    @Override
    public TbUser getByUsername(String username) {
        Example example = new Example(TbUser.class);
        example.createCriteria().andEqualTo("username", username);
        return tbUserMapper.selectOneByExample(example);
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

### [#](https://funtl.com/zh/spring-security-oauth2/基于-RBAC-的自定义认证.html#获取用户权限信息)获取用户权限信息

认证成功后需要给用户授权，具体的权限已经存储在数据库里了，SQL 语句如下：

```sql
SELECT
  p.*
FROM
  tb_user AS u
  LEFT JOIN tb_user_role AS ur
    ON u.id = ur.user_id
  LEFT JOIN tb_role AS r
    ON r.id = ur.role_id
  LEFT JOIN tb_role_permission AS rp
    ON r.id = rp.role_id
  LEFT JOIN tb_permission AS p
    ON p.id = rp.permission_id
WHERE u.id = <yourUserId>
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

### [#](https://funtl.com/zh/spring-security-oauth2/基于-RBAC-的自定义认证.html#自定义认证授权实现类)自定义认证授权实现类

创建一个类，实现 `UserDetailsService` 接口，代码如下：

```java
package com.funtl.oauth2.server.config.service;

import com.funtl.oauth2.server.domain.TbPermission;
import com.funtl.oauth2.server.domain.TbUser;
import com.funtl.oauth2.server.service.TbPermissionService;
import com.funtl.oauth2.server.service.TbUserService;
import org.assertj.core.util.Lists;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.security.core.GrantedAuthority;
import org.springframework.security.core.authority.SimpleGrantedAuthority;
import org.springframework.security.core.userdetails.User;
import org.springframework.security.core.userdetails.UserDetails;
import org.springframework.security.core.userdetails.UserDetailsService;
import org.springframework.security.core.userdetails.UsernameNotFoundException;
import org.springframework.stereotype.Service;

import java.util.List;

/**
 * 自定义用户认证与授权
 * <p>
 * Description:
 * </p>
 *
 * @author Lusifer
 * @version v1.0.0
 * @date 2019-04-04 23:57:04
 * @see com.funtl.oauth2.server.config
 */
@Service
public class UserDetailsServiceImpl implements UserDetailsService {

    @Autowired
    private TbUserService tbUserService;

    @Autowired
    private TbPermissionService tbPermissionService;

    @Override
    public UserDetails loadUserByUsername(String username) throws UsernameNotFoundException {
        // 查询用户信息
        TbUser tbUser = tbUserService.getByUsername(username);
        List<GrantedAuthority> grantedAuthorities = Lists.newArrayList();
        if (tbUser != null) {
            // 获取用户授权
            List<TbPermission> tbPermissions = tbPermissionService.selectByUserId(tbUser.getId());

            // 声明用户授权
            tbPermissions.forEach(tbPermission -> {
                if (tbPermission != null && tbPermission.getEnname() != null) {
                    GrantedAuthority grantedAuthority = new SimpleGrantedAuthority(tbPermission.getEnname());
                    grantedAuthorities.add(grantedAuthority);
                }
            });
        }
        
        // 由框架完成认证工作
        return new User(tbUser.getUsername(), tbUser.getPassword(), grantedAuthorities);
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
55
56
57
58
59
60

### [#](https://funtl.com/zh/spring-security-oauth2/基于-RBAC-的自定义认证.html#服务器安全配置)服务器安全配置

创建一个类继承 `WebSecurityConfigurerAdapter` 并添加相关注解：

- `@Configuration`
- `@EnableWebSecurity`
- `@EnableGlobalMethodSecurity(prePostEnabled = true, securedEnabled = true, jsr250Enabled = true)`：全局方法拦截

```java
package com.funtl.oauth2.server.config;

import com.funtl.oauth2.server.config.service.UserDetailsServiceImpl;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.security.config.annotation.authentication.builders.AuthenticationManagerBuilder;
import org.springframework.security.config.annotation.method.configuration.EnableGlobalMethodSecurity;
import org.springframework.security.config.annotation.web.configuration.EnableWebSecurity;
import org.springframework.security.config.annotation.web.configuration.WebSecurityConfigurerAdapter;
import org.springframework.security.core.userdetails.UserDetailsService;
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

    @Bean
    @Override
    public UserDetailsService userDetailsService() {
        return new UserDetailsServiceImpl();
    }

    @Override
    protected void configure(AuthenticationManagerBuilder auth) throws Exception {
        // 使用自定义认证与授权
        auth.userDetailsService(userDetailsService());
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

## [#](https://funtl.com/zh/spring-security-oauth2/基于-RBAC-的自定义认证.html#application)Application

增加了 Mapper 的包扫描配置

```java
package com.funtl.oauth2;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import tk.mybatis.spring.annotation.MapperScan;

/**
 * 认证服务器，用于获取 Token
 * <p>
 * Description:
 * </p>
 *
 * @author Lusifer
 * @version v1.0.0
 * @date 2019-04-01 16:06:45
 * @see com.funtl.oauth2
 */
@SpringBootApplication
@MapperScan(basePackages = "com.funtl.oauth2.server.mapper")
public class OAuth2ServerApplication {

    public static void main(String[] args) {
        SpringApplication.run(OAuth2ServerApplication.class, args);
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

## [#](https://funtl.com/zh/spring-security-oauth2/基于-RBAC-的自定义认证.html#application-yml)application.yml

增加了 MyBatis 配置

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

mybatis:
  type-aliases-package: com.funtl.oauth2.server.domain
  mapper-locations: classpath:mapper/*.xml
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

## [#](https://funtl.com/zh/spring-security-oauth2/基于-RBAC-的自定义认证.html#访问获取授权码)访问获取授权码

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

## [#](https://funtl.com/zh/spring-security-oauth2/基于-RBAC-的自定义认证.html#通过授权码向服务器申请令牌)通过授权码向服务器申请令牌

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

← [RBAC 基于角色的权限控制](https://funtl.com/zh/spring-security-oauth2/RBAC-基于角色的权限控制.html)[There is no PasswordEncoder mapped ](https://funtl.com/zh/spring-security-oauth2/PasswordEncoder.html)→