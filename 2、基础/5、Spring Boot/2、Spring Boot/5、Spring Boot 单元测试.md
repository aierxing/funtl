# [#](https://funtl.com/zh/spring-boot/Spring-Boot-单元测试.html#spring-boot-单元测试)Spring Boot 单元测试

## [#](https://funtl.com/zh/spring-boot/Spring-Boot-单元测试.html#本节视频)本节视频

- [【视频】微服务框架-SpringBoot-单元测试](https://www.bilibili.com/video/av27784150)

## [#](https://funtl.com/zh/spring-boot/Spring-Boot-单元测试.html#概述)概述

主要是通过 `@RunWith` 和 `@SpringBootTest` 注解来开启单元测试功能

```text
package com.funtl.hello.spring.boot;

import org.junit.Before;
import org.junit.Test;
import org.junit.runner.RunWith;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.boot.test.web.client.TestRestTemplate;
import org.springframework.boot.web.server.LocalServerPort;
import org.springframework.http.ResponseEntity;
import org.springframework.test.context.junit4.SpringRunner;

import java.net.URL;

import static org.hamcrest.CoreMatchers.equalTo;
import static org.junit.Assert.assertThat;

@RunWith(SpringRunner.class)
@SpringBootTest(classes = HelloSpringBootApplication.class, webEnvironment = SpringBootTest.WebEnvironment.RANDOM_PORT)
public class HelloSpringBootApplicationTests {

    @LocalServerPort
    private int port;

    private URL base;

    @Autowired
    private TestRestTemplate template;

    @Before
    public void setUp() throws Exception {
        this.base = new URL("http://localhost:" + port + "/");
    }

    @Test
    public void contextLoads() {
        ResponseEntity<String> response = template.getForEntity(base.toString(), String.class);
        assertThat(response.getBody(), equalTo("Hello Spring Boot"));
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

运行它会先启动 Spring Boot 工程，再启动单元测试

上次更新: 2018-12-31 18:51:48

← 