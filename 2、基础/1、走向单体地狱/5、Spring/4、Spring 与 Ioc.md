# [#](https://funtl.com/zh/spring/Spring-与-IoC.html#spring-与-ioc)Spring 与 IoC

## [#](https://funtl.com/zh/spring/Spring-与-IoC.html#本节视频)本节视频

- [【视频】基础框架入门-Spring-Spring 与 IoC](https://www.bilibili.com/video/av24509478/)

## [#](https://funtl.com/zh/spring/Spring-与-IoC.html#概述)概述

控制反转（IoC，Inversion of Control），是一个概念，是一种思想。指将传统上由程序代码直接操控的对象调用权交给容器，通过容器来实现对象的装配和管理。控制反转就是对对象控制权的转移，从程序代码本身反转到了外部容器。

IoC 是一个概念，是一种思想，其实现方式多种多样。当前比较流行的实现方式有两种： 依赖注入和依赖查找。依赖注入方式应用更为广泛。

- 依赖查找：Dependency Lookup，DL，容器提供回调接口和上下文环境给组件，程序代码则需要提供具体的查找方式。比较典型的是依赖于 JNDI 系统的查找。
- **依赖注入：**Dependency Injection，DI，程序代码不做定位查询，这些工作由容器自行完成。

依赖注入 DI 是指程序运行过程中，若需要调用另一个对象协助时，无须在代码中创建被调用者，而是依赖于外部容器，由外部容器创建后传递给程序。

Spring 的依赖注入对调用者与被调用者几乎没有任何要求，完全支持 POJO 之间依赖关系的管理。

**依赖注入是目前最优秀的解耦方式**。依赖注入让 Spring 的 Bean 之间以配置文件的方式组织在一起，而不是以硬编码的方式耦合在一起的。

上次更新: 2018-12-29 1:45:15