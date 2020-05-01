# [#](https://funtl.com/zh/spring-cloud-alibaba-dubbo-vue/基于-Docker-安装-Nacos.html#基于-docker-安装-nacos)基于 Docker 安装 Nacos

## [#](https://funtl.com/zh/spring-cloud-alibaba-dubbo-vue/基于-Docker-安装-Nacos.html#概述)概述

关于 Nacos 相关说明请参考我博客 [Spring Cloud Alibaba](https://funtl.com/zh/spring-cloud-alibaba/) 章节的 [服务注册与发现](https://funtl.com/zh/spring-cloud-alibaba/服务注册与发现.html)

## [#](https://funtl.com/zh/spring-cloud-alibaba-dubbo-vue/基于-Docker-安装-Nacos.html#安装)安装

```bash
git clone https://github.com/nacos-group/nacos-docker.git
cd nacos-docker
docker-compose -f example/standalone-mysql.yaml up
```

1
2
3

## [#](https://funtl.com/zh/spring-cloud-alibaba-dubbo-vue/基于-Docker-安装-Nacos.html#访问)访问

http://127.0.0.1:8848/nacos/

**注：从 0.8.0 版本开始，需要登录才可访问，默认账号密码为 nacos/nacos**

上次更新: 2019-3-16 10:19:40

← [简介](https://funtl.com/zh/spring-cloud-alibaba-dubbo-vue/)[创建项目工程 ](https://funtl.com/zh/spring-cloud-alibaba-dubbo-vue/创建项目工程.html)→