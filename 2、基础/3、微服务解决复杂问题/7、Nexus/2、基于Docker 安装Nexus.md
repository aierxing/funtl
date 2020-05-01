# [#](https://funtl.com/zh/nexus/基于-Docker-安装-Nexus.html#基于-docker-安装-nexus)基于 Docker 安装 Nexus

我们使用 Docker 来安装和运行 Nexus，`docker-compose.yml` 配置如下：

```text
version: '3.1'
services:
  nexus:
    restart: always
    image: sonatype/nexus3
    container_name: nexus
    ports:
      - 8081:8081
    volumes:
      - /usr/local/docker/nexus/data:/nexus-data
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

*注：* 启动时如果出现权限问题可以使用：`chmod 777 /usr/local/docker/nexus/data` 赋予数据卷目录可读可写的权限

## [#](https://funtl.com/zh/nexus/基于-Docker-安装-Nexus.html#登录控制台验证安装)登录控制台验证安装

地址：http://ip:port/ 用户名：admin 密码：admin123

![img](https://funtl.com/assets/Lusifer1521047001.png)

上次更新: 2019-4-19 19:23:15

← 