# [#](https://funtl.com/zh/apache-dubbo-ci/配置-Jenkins.html#配置-jenkins)配置 Jenkins

## [#](https://funtl.com/zh/apache-dubbo-ci/配置-Jenkins.html#本节视频)本节视频

- [【视频】Dubbo 实现微服务架构-持续交付-配置 Jenkins](https://www.bilibili.com/video/av34863008/)

## [#](https://funtl.com/zh/apache-dubbo-ci/配置-Jenkins.html#配置-jdk-maven)配置 JDK & Maven

- 上传 JDK 和 Maven 的 tar 包到服务器（容器数据卷目录）
- `Manage Jenkins` -> `Global Tool Configuration`
- 安装 JDK（`JAVA_HOME` **的路径是宿主机目录，切记！不明白的看视频！**）

```text
/var/jenkins_home/jdk1.8.0_152
```

1

![img](https://funtl.com/assets/Lusifer_20181029023809.png)

- 安装 Maven（`MAVEN_HOME` **的路径是宿主机目录，切记！不明白的看视频！**）

```text
/var/jenkins_home/apache-maven-3.5.3
```

1

![img](https://funtl.com/assets/Lusifer_20181029024653.png)

- 别忘记保存

## [#](https://funtl.com/zh/apache-dubbo-ci/配置-Jenkins.html#配置本地化（显示中文）)配置本地化（显示中文）

- 安装 `Locale` 插件

![img](https://funtl.com/assets/Lusifer_20181029032817.png)

- `Manage Jenkins` -> `Configure System` -> `Locale`

![img](https://funtl.com/assets/Lusifer_20181029033127.png)

- 本地化效果图

![img](https://funtl.com/assets/Lusifer_20181029042305.png)

## [#](https://funtl.com/zh/apache-dubbo-ci/配置-Jenkins.html#安装动态参数插件)安装动态参数插件

该插件的主要目的是为了方便我们后面在做项目构建时可以按照版本进行构建（支持一键回滚哦）

![img](https://funtl.com/assets/Lusifer_20181029050930.png)

上次更新: 2019-4-19 19:23:15

← [基于 Docker 安装 Jenkins](https://funtl.com/zh/apache-dubbo-ci/基于-Docker-安装-Jenkins.html)[持续交付实战用户管理服务 ](https://funtl.com/zh/apache-dubbo-ci/持续交付实战用户管理服务.html)→