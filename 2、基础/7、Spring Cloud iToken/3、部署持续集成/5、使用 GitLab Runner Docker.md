# 使用 GitLab Runner Docker

## [#](https://funtl.com/zh/spring-cloud-itoken-ci/使用-GitLab-Runner-Docker.html#本节视频)本节视频

- [【视频】项目实战-iToken-部署持续集成-使用 GitLab Runner Docker](https://www.bilibili.com/video/av28384215)
- [【视频】项目实战-iToken-部署持续集成-第一个 GitLab Runner 脚本](https://www.bilibili.com/video/av28384268)
- [【视频】项目实战-iToken-部署持续集成-实战分布式配置中心](https://www.bilibili.com/video/av28384251)
- [【视频】项目实战-iToken-部署持续集成-实战服务注册与发现](https://www.bilibili.com/video/av28384230)

## [#](https://funtl.com/zh/spring-cloud-itoken-ci/使用-GitLab-Runner-Docker.html#概述)概述

为了配置方便，我们使用 `docker` 来部署 `GitLab Runner`

## [#](https://funtl.com/zh/spring-cloud-itoken-ci/使用-GitLab-Runner-Docker.html#环境准备)环境准备

- 创建工作目录 `/usr/local/docker/runner`
- 创建构建目录 `/usr/local/docker/runner/environment`
- 下载 `jdk-8u152-linux-x64.tar.gz` 并复制到 `/usr/local/docker/runner/environment`

## [#](https://funtl.com/zh/spring-cloud-itoken-ci/使用-GitLab-Runner-Docker.html#dockerfile)Dockerfile

在 `/usr/local/docker/runner/environment` 目录下创建 `Dockerfile`

```text
FROM gitlab/gitlab-runner:v11.0.2
MAINTAINER Lusifer <topsale@vip.qq.com>

# 修改软件源
RUN echo 'deb http://mirrors.aliyun.com/ubuntu/ xenial main restricted universe multiverse' > /etc/apt/sources.list && \
    echo 'deb http://mirrors.aliyun.com/ubuntu/ xenial-security main restricted universe multiverse' >> /etc/apt/sources.list && \
    echo 'deb http://mirrors.aliyun.com/ubuntu/ xenial-updates main restricted universe multiverse' >> /etc/apt/sources.list && \
    echo 'deb http://mirrors.aliyun.com/ubuntu/ xenial-backports main restricted universe multiverse' >> /etc/apt/sources.list && \
    apt-get update -y && \
    apt-get clean

# 安装 Docker
RUN apt-get -y install apt-transport-https ca-certificates curl software-properties-common && \
    curl -fsSL http://mirrors.aliyun.com/docker-ce/linux/ubuntu/gpg | apt-key add - && \
    add-apt-repository "deb [arch=amd64] http://mirrors.aliyun.com/docker-ce/linux/ubuntu $(lsb_release -cs) stable" && \
    apt-get update -y && \
    apt-get install -y docker-ce
COPY daemon.json /etc/docker/daemon.json

# 安装 Docker Compose
WORKDIR /usr/local/bin
RUN wget https://raw.githubusercontent.com/topsale/resources/master/docker/docker-compose
RUN chmod +x docker-compose

# 安装 Java
RUN mkdir -p /usr/local/java
WORKDIR /usr/local/java
COPY jdk-8u152-linux-x64.tar.gz /usr/local/java
RUN tar -zxvf jdk-8u152-linux-x64.tar.gz && \
    rm -fr jdk-8u152-linux-x64.tar.gz

# 安装 Maven
RUN mkdir -p /usr/local/maven
WORKDIR /usr/local/maven
RUN wget https://raw.githubusercontent.com/topsale/resources/master/maven/apache-maven-3.5.3-bin.tar.gz
# COPY apache-maven-3.5.3-bin.tar.gz /usr/local/maven
RUN tar -zxvf apache-maven-3.5.3-bin.tar.gz && \
    rm -fr apache-maven-3.5.3-bin.tar.gz
# COPY settings.xml /usr/local/maven/apache-maven-3.5.3/conf/settings.xml

# 配置环境变量
ENV JAVA_HOME /usr/local/java/jdk1.8.0_152
ENV MAVEN_HOME /usr/local/maven/apache-maven-3.5.3
ENV PATH $PATH:$JAVA_HOME/bin:$MAVEN_HOME/bin

WORKDIR /
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

## [#](https://funtl.com/zh/spring-cloud-itoken-ci/使用-GitLab-Runner-Docker.html#daemon-json)daemon.json

在 `/usr/local/docker/runner/environment` 目录下创建 `daemon.json`，用于配置加速器和仓库地址

```text
{
  "registry-mirrors": [
    "https://registry.docker-cn.com"
  ],
  "insecure-registries": [
    "192.168.75.131:5000"
  ]
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

## [#](https://funtl.com/zh/spring-cloud-itoken-ci/使用-GitLab-Runner-Docker.html#docker-compose-yml)docker-compose.yml

在 `/usr/local/docker/runner` 目录下创建 `docker-compose.yml`

```text
version: '3.1'
services:
  gitlab-runner:
    build: environment
    restart: always
    container_name: gitlab-runner
    privileged: true
    volumes:
      - /usr/local/docker/runner/config:/etc/gitlab-runner
      - /var/run/docker.sock:/var/run/docker.sock
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

## [#](https://funtl.com/zh/spring-cloud-itoken-ci/使用-GitLab-Runner-Docker.html#注册-runner)注册 Runner

```text
docker exec -it gitlab-runner gitlab-runner register

# 输入 GitLab 地址
Please enter the gitlab-ci coordinator URL (e.g. https://gitlab.com/):
http://192.168.75.146:8080/

# 输入 GitLab Token
Please enter the gitlab-ci token for this runner:
1Lxq_f1NRfCfeNbE5WRh

# 输入 Runner 的说明
Please enter the gitlab-ci description for this runner:
可以为空

# 设置 Tag，可以用于指定在构建规定的 tag 时触发 ci
Please enter the gitlab-ci tags for this runner (comma separated):
deploy

# 这里选择 true ，可以用于代码上传后直接执行
Whether to run untagged builds [true/false]:
true

# 这里选择 false，可以直接回车，默认为 false
Whether to lock Runner to current project [true/false]:
false

# 选择 runner 执行器，这里我们选择的是 shell
Please enter the executor: virtualbox, docker+machine, parallels, shell, ssh, docker-ssh+machine, kubernetes, docker, docker-ssh:
shell
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

## [#](https://funtl.com/zh/spring-cloud-itoken-ci/使用-GitLab-Runner-Docker.html#附：项目配置-dockerfile-案例)附：项目配置 Dockerfile 案例

```text
FROM openjdk:8-jre

MAINTAINER Lusifer <topsale@vip.qq.com>

ENV APP_VERSION 1.0.0-SNAPSHOT
ENV DOCKERIZE_VERSION v0.6.1
RUN wget https://github.com/jwilder/dockerize/releases/download/$DOCKERIZE_VERSION/dockerize-linux-amd64-$DOCKERIZE_VERSION.tar.gz \
    && tar -C /usr/local/bin -xzvf dockerize-linux-amd64-$DOCKERIZE_VERSION.tar.gz \
    && rm dockerize-linux-amd64-$DOCKERIZE_VERSION.tar.gz

RUN mkdir /app

COPY itoken-eureka-$APP_VERSION.jar /app/app.jar
ENTRYPOINT ["dockerize", "-timeout", "5m", "-wait", "tcp://192.168.75.128:8888", "java", "-Djava.security.egd=file:/dev/./urandom", "-jar", "/app/app.jar", "--spring.profiles.active=prod"]

EXPOSE 8761
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

上次更新: 2018-12-31 18:51:48

← [使用 GitLab Runner](https://funtl.com/zh/spring-cloud-itoken-ci/使用-GitLab-Runner.html)