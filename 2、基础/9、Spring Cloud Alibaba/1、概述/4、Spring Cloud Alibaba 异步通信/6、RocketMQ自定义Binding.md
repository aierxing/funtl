# [#](https://funtl.com/zh/spring-cloud-alibaba/RocketMQ-自定义-Binding.html#rocketmq-自定义-binding)RocketMQ 自定义 Binding

## [#](https://funtl.com/zh/spring-cloud-alibaba/RocketMQ-自定义-Binding.html#概述)概述

在实际生产中，我们需要发布和订阅的消息可能不止一种 Topic ，故此时就需要使用自定义 Binding 来帮我们实现多 Topic 的发布和订阅功能

## [#](https://funtl.com/zh/spring-cloud-alibaba/RocketMQ-自定义-Binding.html#生产者)生产者

自定义 Output 接口，代码如下：

```java
public interface MySource {
    @Output("output1")
    MessageChannel output1();

    @Output("output2")
    MessageChannel output2();
}
```

1
2
3
4
5
6
7

发布消息的案例代码如下：

```java
@Autowired
private MySource source;

public void send(String msg) throws Exception {
    source.output1().send(MessageBuilder.withPayload(msg).build());
}
```

1
2
3
4
5
6

## [#](https://funtl.com/zh/spring-cloud-alibaba/RocketMQ-自定义-Binding.html#消费者)消费者

自定义 Input 接口，代码如下：

```java
public interface MySink {
    @Input("input1")
    SubscribableChannel input1();

    @Input("input2")
    SubscribableChannel input2();

    @Input("input3")
    SubscribableChannel input3();

    @Input("input4")
    SubscribableChannel input4();
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

接收消息的案例代码如下：

```java
@StreamListener("input1")
public void receiveInput1(String receiveMsg) {
    System.out.println("input1 receive: " + receiveMsg);
}
```

1
2
3
4

## [#](https://funtl.com/zh/spring-cloud-alibaba/RocketMQ-自定义-Binding.html#application)Application

配置 Input 和 Output 的 Binding 信息并配合 `@EnableBinding` 注解使其生效，代码如下：

```java
@SpringBootApplication
@EnableBinding({ MySource.class, MySink.class })
public class RocketMQApplication {
	public static void main(String[] args) {
		SpringApplication.run(RocketMQApplication.class, args);
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

## [#](https://funtl.com/zh/spring-cloud-alibaba/RocketMQ-自定义-Binding.html#application-yml)application.yml

### [#](https://funtl.com/zh/spring-cloud-alibaba/RocketMQ-自定义-Binding.html#生产者-2)生产者

```yaml
spring:
  application:
    name: rocketmq-provider
  cloud:
    stream:
      rocketmq:
        binder:
          namesrv-addr: 192.168.10.149:9876
      bindings:
        output1: {destination: test-topic1, content-type: application/json}
        output2: {destination: test-topic2, content-type: application/json}
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

### [#](https://funtl.com/zh/spring-cloud-alibaba/RocketMQ-自定义-Binding.html#消费者-2)消费者

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
        input1: {destination: test-topic1, content-type: text/plain, group: test-group, consumer.maxAttempts: 1}
        input2: {destination: test-topic1, content-type: text/plain, group: test-group, consumer.maxAttempts: 1}
        input3: {destination: test-topic2, content-type: text/plain, group: test-group, consumer.maxAttempts: 1}
        input4: {destination: test-topic2, content-type: text/plain, group: test-group, consumer.maxAttempts: 1}
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

上次更新: 2019-1-16 5:53:37

← [RocketMQ 消费者](https://funtl.com/zh/spring-cloud-alibaba/RocketMQ-消费者.html)