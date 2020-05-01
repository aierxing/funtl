# [#](https://funtl.com/zh/maven/Maven-安装配置.html#maven-安装配置)Maven 安装配置

## [#](https://funtl.com/zh/maven/Maven-安装配置.html#本节视频)本节视频

- [【视频】使用 Maven 构建应用-Maven 安装配置](https://www.bilibili.com/video/av24451908/)

## [#](https://funtl.com/zh/maven/Maven-安装配置.html#概述)概述

想要安装 Apache Maven 在 Windows 系统上, 需要下载 Maven 的 zip 文件，并将其解压到你想安装的目录，并配置 Windows 环境变量。

注意：请尽量使用 JDK 1.8 及以上版本

## [#](https://funtl.com/zh/maven/Maven-安装配置.html#jdk-和-java-home)JDK 和 JAVA_HOME

确保已安装 JDK，并设置 `JAVA_HOME` 环境变量到 Windows 环境变量。

![img](https://funtl.com/assets/Lusifer1511451715.png)

## [#](https://funtl.com/zh/maven/Maven-安装配置.html#下载-apache-maven)下载 Apache Maven

下载地址：http://maven.apache.org/download.cgi

![img](https://funtl.com/assets/Lusifer1511451890.png)

下载 Maven 的 zip 文件，例如： apache-maven-3.5.2-bin.zip，将它解压到你要安装 Maven 的文件夹。假设你解压缩到文件夹 – D:\apache-maven-3.5.2

![img](https://funtl.com/assets/Lusifer1511452022.png)

注意：在这一步，只是文件夹和文件，安装不是必需的。

## [#](https://funtl.com/zh/maven/Maven-安装配置.html#添加-maven-home)添加 MAVEN_HOME

添加 `MAVEN_HOME` 环境变量到 Windows 环境变量，并将其指向你的 Maven 文件夹。

![img](https://funtl.com/assets/Lusifer1511452135.png)

## [#](https://funtl.com/zh/maven/Maven-安装配置.html#添加到环境变量-path)添加到环境变量 - PATH

![img](https://funtl.com/assets/Lusifer1511452190.png)

## [#](https://funtl.com/zh/maven/Maven-安装配置.html#验证)验证

使用命令：`mvn -version`

输出：

```shell
C:\Users\Lusifer>mvn -version
Apache Maven 3.5.2 (138edd61fd100ec658bfa2d307c43b76940a5d7d; 2017-10-18T15:58:13+08:00)
Maven home: D:\apache-maven-3.5.2\bin\..
Java version: 1.8.0_152, vendor: Oracle Corporation
Java home: C:\Program Files\Java\jdk1.8.0_152\jre
Default locale: zh_CN, platform encoding: GBK
OS name: "windows 10", version: "10.0", arch: "amd64", family: "windows"
```

上次更新: 2019-4-19 19:23:15