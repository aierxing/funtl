# [#](https://funtl.com/zh/spring-cloud-alibaba/Nacos-Config-多环境的配置.html#nacos-config-多环境的配置)Nacos Config 多环境的配置

## [#](https://funtl.com/zh/spring-cloud-alibaba/Nacos-Config-多环境的配置.html#本节视频)本节视频

- [【视频】Spring Cloud Alibaba-Nacos-分布式配置中心-多环境配置](https://www.bilibili.com/video/av40735056/)

## [#](https://funtl.com/zh/spring-cloud-alibaba/Nacos-Config-多环境的配置.html#spring-boot-profile)Spring Boot Profile

我们在做项目开发的时候，生产环境和测试环境的一些配置可能会不一样，有时候一些功能也可能会不一样，所以我们可能会在上线的时候手工修改这些配置信息。但是 Spring 中为我们提供了 Profile 这个功能。我们只需要在启动的时候添加一个虚拟机参数，激活自己环境所要用的 Profile 就可以了。

操作起来很简单，只需要为不同的环境编写专门的配置文件，如：`application-dev.yml`、`application-prod.yml`， 启动项目时只需要增加一个命令参数 `--spring.profiles.active=环境配置` 即可，启动命令如下：

```bash
java -jar hello-spring-cloud-alibaba-nacos-provider-1.0.0-SNAPSHOT.jar --spring.profiles.active=prod
```

1

## [#](https://funtl.com/zh/spring-cloud-alibaba/Nacos-Config-多环境的配置.html#nacos-config-profile)Nacos Config Profile

spring-cloud-starter-alibaba-nacos-config 在加载配置的时候，不仅仅加载了以 dataid 为 `${spring.application.name}.${file-extension:properties}` 为前缀的基础配置，还加载了 dataid 为 `${spring.application.name}-${profile}.${file-extension:properties}` 的基础配置。在日常开发中如果遇到多套环境下的不同配置，可以通过 Spring 提供的 `${spring.profiles.active}` 这个配置项来配置。

此处我们以之前创建的 [**服务提供者**](https://funtl.com/zh/spring-cloud-alibaba/创建服务提供者.html#创建服务提供者) 项目为例

### [#](https://funtl.com/zh/spring-cloud-alibaba/Nacos-Config-多环境的配置.html#在-nacos-server-中增加配置)在 Nacos Server 中增加配置

增加一个名为 `nacos-provider-config-prod.yaml` 的配置

![img](https://funtl.com/assets1/Lusifer_20190111041121.png)

**注意：此时，我将配置文件中的端口由 `8081` -> `8082`**

### [#](https://funtl.com/zh/spring-cloud-alibaba/Nacos-Config-多环境的配置.html#在项目中增加配置)在项目中增加配置

增加一个名为 `bootstrap-prod.properties` 的配置文件，内容如下：

```properties
spring.profiles.active=prod
spring.application.name=nacos-provider-config
spring.cloud.nacos.config.file-extension=yaml
spring.cloud.nacos.config.server-addr=127.0.0.1:8848
```

1
2
3
4

主要增加了 `spring.profiles.active=prod` 配置，用于指定访问 Nacos Server 中的 `nacos-provider-config-prod.yaml` 配置

## [#](https://funtl.com/zh/spring-cloud-alibaba/Nacos-Config-多环境的配置.html#启动应用程序)启动应用程序

此时我们有两个配置文件，分别为 `bootstrap.properties` 和 `bootstrap-prod.properties` ，我们需要指定启动时加载哪一个配置文件，操作流程如下：

- `Run` -> `Edit Configurations..`

![img](https://funtl.com/assets1/Lusifer_20190111043201.png)

- 设置需要激活的配置

![img](https://funtl.com/assets1/Lusifer_20190111043322.png)

- 观察日志，判断是否成功加载配置

![img](https://funtl.com/assets1/Lusifer_20190111043538.png)

上次更新: 2019-1-15 2:45:51

← [Nacos Config 客户端的使用](https://funtl.com/zh/spring-cloud-alibaba/Nacos-Config-客户端的使用.html)[为什么需要链路追踪 ](https://funtl.com/zh/spring-cloud-alibaba/为什么需要链路追踪.html)→