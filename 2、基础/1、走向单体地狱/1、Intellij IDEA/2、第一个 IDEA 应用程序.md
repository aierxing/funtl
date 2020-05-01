# [#](https://funtl.com/zh/idea/第一个-IDEA-应用程序.html#第一个-idea-应用程序)第一个 IDEA 应用程序

## [#](https://funtl.com/zh/idea/第一个-IDEA-应用程序.html#本节视频)本节视频

- [【视频】使用 Intellij IDEA-第一个 IDEA 应用程序](https://www.bilibili.com/video/av24437719/)

## [#](https://funtl.com/zh/idea/第一个-IDEA-应用程序.html#新建-java-web-项目)新建 Java Web 项目

打开 `IDEA` -> `Create New Project`

![img](https://funtl.com/assets/Lusifer1528017464.png)

选择 `Java` -> `Java EE` -> `Web Application`

![img](https://funtl.com/assets/Lusifer1528017638.png)

## [#](https://funtl.com/zh/idea/第一个-IDEA-应用程序.html#选择工作空间)选择工作空间

项目命名后选择存放的工作空间，项目就创建完成了

![img](https://funtl.com/assets/Lusifer1528018627.png)

## [#](https://funtl.com/zh/idea/第一个-IDEA-应用程序.html#配置-jdk)配置 JDK

选择 `File` -> `Project Structure...`

![img](https://funtl.com/assets/Lusifer1528018777.png)

选择 JDK 的安装路径即可

![img](https://funtl.com/assets/Lusifer1528018883.png)

## [#](https://funtl.com/zh/idea/第一个-IDEA-应用程序.html#配置-tomcat)配置 Tomcat

选择 `Run` -> `Edit Configurations...`

![img](https://funtl.com/assets/Lusifer1528019007.png)

选择 `+` 号 -> `Tomcat Server` -> `Local`

![img](https://funtl.com/assets/Lusifer1528019058.png)

选择 Tomcat 的安装路径即可

![img](https://funtl.com/assets/Lusifer1528019181.png)

## [#](https://funtl.com/zh/idea/第一个-IDEA-应用程序.html#配置-tomcat-本地部署)配置 Tomcat 本地部署

继续上一步，选择 `Deployment` -> `+` 号 -> `Artifact...`

![img](https://funtl.com/assets/Lusifer1528019373.png)

![img](https://funtl.com/assets/Lusifer1528019572.png)

选择 `Server` 配置自动更新

![img](https://funtl.com/assets/Lusifer1528020264.png)

## [#](https://funtl.com/zh/idea/第一个-IDEA-应用程序.html#测试运行)测试运行

选择需要运行的项目，点击 `运行` 图标

![img](https://funtl.com/assets/Lusifer1528020413.png)

浏览器打开：http://localhost:8080 显示如下

```java
$END$
```

上次更新: 2019-4-19 19:23:15