# [#](https://funtl.com/zh/spring-cloud-alibaba/RocketMQ-消费者.html#rocketmq-消费者)RocketMQ 消费者

## [#](https://funtl.com/zh/spring-cloud-alibaba/RocketMQ-消费者.html#本节视频)本节视频

- [【视频】Spring Cloud Alibaba-RocketMQ-消费者](https://www.bilibili.com/video/av40868997/)

## [#](https://funtl.com/zh/spring-cloud-alibaba/RocketMQ-消费者.html#pom)POM

主要增加了 `org.springframework.cloud:spring-cloud-starter-stream-rocketmq` 依赖

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <parent>
        <groupId>com.funtl</groupId>
        <artifactId>hello-spring-cloud-alibaba-dependencies</artifactId>
        <version>1.0.0-SNAPSHOT</version>
        <relativePath>../hello-spring-cloud-alibaba-dependencies/pom.xml</relativePath>
    </parent>

    <artifactId>hello-spring-cloud-alibaba-rocketmq-consumer</artifactId>
    <packaging>jar</packaging>

    <name>hello-spring-cloud-alibaba-rocketmq-consumer</name>
    <url>http://www.funtl.com</url>
    <inceptionYear>2018-Now</inceptionYear>

    <dependencies>
        <!-- Spring Boot Begin -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-actuator</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
        <!-- Spring Boot End -->

        <!-- Spring Cloud Begin -->
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-stream-rocketmq</artifactId>
        </dependency>
        <!-- Spring Cloud End -->
    </dependencies>

    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
                <configuration>
                    <mainClass>com.funtl.hello.spring.cloud.alibaba.rocketmq.consumer.RocketMQConsumerApplication</mainClass>
                </configuration>
            </plugin>
        </plugins>
    </build>
</project>
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

## [#](https://funtl.com/zh/spring-cloud-alibaba/RocketMQ-消费者.html#消息消费者服务)消息消费者服务

主要使用 `@StreamListener("input")` 注解来订阅从名为 `input` 的 Binding 中接收的消息

```java
package com.funtl.hello.spring.cloud.alibaba.rocketmq.consumer.receive;

import org.springframework.cloud.stream.annotation.StreamListener;
import org.springframework.stereotype.Service;

@Service
public class ConsumerReceive {

    @StreamListener("input")
    public void receiveInput(String message) {
        System.out.println("Receive input: " + message);
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

## [#](https://funtl.com/zh/spring-cloud-alibaba/RocketMQ-消费者.html#application)Application

配置 Input(`Sink.class`) 的 Binding 信息并配合 `@EnableBinding` 注解使其生效

```java
package com.funtl.hello.spring.cloud.alibaba.rocketmq.consumer;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.stream.annotation.EnableBinding;
import org.springframework.cloud.stream.messaging.Sink;

@SpringBootApplication
@EnableBinding({Sink.class})
public class RocketMQConsumerApplication {
    public static void main(String[] args) {
        SpringApplication.run(RocketMQConsumerApplication.class, args);
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

## [#](https://funtl.com/zh/spring-cloud-alibaba/RocketMQ-消费者.html#application-yml)application.yml

```yaml
spring:
  application:
    name: rocketmq-consumer
  cloud:
    stream:
      rocketmq:
        binder:
          namesrv-addr: 192.168.10.149:9876
        bindings:
          input: {consumer.orderly: true}
      bindings:
        input: {destination: test-topic, content-type: text/plain, group: test-group, consumer.maxAttempts: 1}

server:
  port: 9094

management:
  endpoints:
    web:
      exposure:
        include: '*'
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

运行成功后即可在控制台接收到消息：`Receive input: Hello RocketMQ`

上次更新: 2019-1-17 2:26:53

← [RocketMQ 生产者](https://funtl.com/zh/spring-cloud-alibaba/RocketMQ-生产者.html)[RocketMQ 自定义 Binding ](https://funtl.com/zh/spring-cloud-alibaba/RocketMQ-自定义-Binding.html)→