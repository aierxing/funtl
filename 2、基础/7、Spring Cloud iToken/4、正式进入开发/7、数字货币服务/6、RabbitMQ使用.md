# [#](https://funtl.com/zh/spring-cloud-itoken-codeing/RabbitMQ-使用.html#rabbitmq-使用)RabbitMQ 使用

## [#](https://funtl.com/zh/spring-cloud-itoken-codeing/RabbitMQ-使用.html#本节视频)本节视频

- [【视频】项目实战-iToken-消息队列-RabbitMQ 使用](https://www.bilibili.com/video/av29770235)

## [#](https://funtl.com/zh/spring-cloud-itoken-codeing/RabbitMQ-使用.html#生产者)生产者

创建一个名为 `spring-boot-amqp-provider` 的生产者项目

### [#](https://funtl.com/zh/spring-cloud-itoken-codeing/RabbitMQ-使用.html#application-yml)application.yml

```text
spring:
  application:
    name: spring-boot-amqp
  rabbitmq:
    host: 192.168.75.133
    port: 5672
    username: rabbit
    password: 123456
```

1
2
3
4
5
6
7
8

### [#](https://funtl.com/zh/spring-cloud-itoken-codeing/RabbitMQ-使用.html#创建队列配置)创建队列配置

```text
package com.lusifer.spring.boot.amqp.config;

import org.springframework.amqp.core.Queue;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

/**
 * 队列配置
 * <p>Title: RabbitMQConfiguration</p>
 * <p>Description: </p>
 *
 * @author Lusifer
 * @version 1.0.0
 * @date 2018/5/1 22:39
 */
@Configuration
public class RabbitMQConfiguration {
    @Bean
    public Queue queue() {
        return new Queue("helloRabbit");
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

### [#](https://funtl.com/zh/spring-cloud-itoken-codeing/RabbitMQ-使用.html#创建消息提供者)创建消息提供者

```text
package com.lusifer.spring.boot.amqp.provider;

import org.springframework.amqp.core.AmqpTemplate;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Component;

import java.util.Date;

/**
 * 提供者
 * <p>Title: HelloRabbitProvider</p>
 * <p>Description: </p>
 *
 * @author Lusifer
 * @version 1.0.0
 * @date 2018/5/1 22:39
 */
@Component
public class HelloRabbitProvider {
    @Autowired
    private AmqpTemplate amqpTemplate;

    public void send() {
        String context = "hello" + new Date();
        System.out.println("Provider: " + context);
        amqpTemplate.convertAndSend("helloRabbit", context);
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

### [#](https://funtl.com/zh/spring-cloud-itoken-codeing/RabbitMQ-使用.html#创建测试用例)创建测试用例

```text
package com.lusifer.spring.boot.amqp.test;

import com.lusifer.spring.boot.amqp.Application;
import com.lusifer.spring.boot.amqp.provider.HelloRabbitProvider;
import org.junit.Test;
import org.junit.runner.RunWith;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.test.context.junit4.SpringRunner;

@RunWith(SpringRunner.class)
@SpringBootTest(classes = Application.class)
public class AmqpTest {
    @Autowired
    private HelloRabbitProvider helloRabbitProvider;

    @Test
    public void testSender() {
        for (int i = 0; i < 10; i++) {
            helloRabbitProvider.send();
        }
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

## [#](https://funtl.com/zh/spring-cloud-itoken-codeing/RabbitMQ-使用.html#消费者)消费者

创建一个名为 `spring-boot-amqp-consumer` 的消费者项目

### [#](https://funtl.com/zh/spring-cloud-itoken-codeing/RabbitMQ-使用.html#application-yml-2)application.yml

```text
spring:
  application:
    name: spring-boot-amqp-consumer
  rabbitmq:
    host: 192.168.75.133
    port: 5672
    username: rabbit
    password: 123456
```

1
2
3
4
5
6
7
8

### [#](https://funtl.com/zh/spring-cloud-itoken-codeing/RabbitMQ-使用.html#创建消息消费者)创建消息消费者

```text
package com.lusifer.spring.boot.amqp.consumer;

import org.springframework.amqp.rabbit.annotation.RabbitHandler;
import org.springframework.amqp.rabbit.annotation.RabbitListener;
import org.springframework.stereotype.Component;

@Component
@RabbitListener(queues = "helloRabbit")
public class HelloRabbitConsumer {
    @RabbitHandler
    public void process(String message) {
        System.out.println("Consumer: " + message);
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

上次更新: 2018-12-31 18:51:48

← [RabbitMQ 安装](https://funtl.com/zh/spring-cloud-itoken-codeing/RabbitMQ-安装.html)[Quartz 使用 ](https://funtl.com/zh/spring-cloud-itoken-codeing/Quartz-使用.html)→