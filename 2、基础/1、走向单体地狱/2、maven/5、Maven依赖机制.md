# [#](https://funtl.com/zh/maven/Maven-依赖机制.html#maven-依赖机制)Maven 依赖机制

## [#](https://funtl.com/zh/maven/Maven-依赖机制.html#本节视频)本节视频

- [【视频】使用 Maven 构建应用-Maven 依赖机制](https://www.bilibili.com/video/av24455290/)

## [#](https://funtl.com/zh/maven/Maven-依赖机制.html#概述)概述

在 Maven 依赖机制的帮助下自动下载所有必需的依赖库，并保持版本升级。让我们看一个案例研究，以了解它是如何工作的。假设你想使用 Log4j 作为项目的日志。这里你要做什么？

## [#](https://funtl.com/zh/maven/Maven-依赖机制.html#传统方式)传统方式

- 访问 http://logging.apache.org/log4j/
- 下载 Log4j 的 jar 库
- 复制 jar 到项目类路径
- 手动将其包含到项目的依赖
- 所有的管理需要一切由自己做

如果有 Log4j 版本升级，则需要重复上述步骤一次。

## [#](https://funtl.com/zh/maven/Maven-依赖机制.html#maven-的方式)Maven 的方式

- 你需要知道 log4j 的 Maven 坐标，例如：

```xml
<groupId>log4j</groupId>
<artifactId>log4j</artifactId>
<version>1.2.17</version>
```

- 它会自动下载 log4j 的 1.2.17 版本库
- 声明 Maven 的坐标转换成 `pom.xml` 文件

```xml
<dependencies>
    <dependency>
	<groupId>log4j</groupId>
	<artifactId>log4j</artifactId>
	<version>1.2.17</version>
    </dependency>
</dependencies>
```

- 当 Maven 编译或构建，log4j 的 jar 会自动下载，并把它放到 Maven 本地存储库
- 所有由 Maven 管理

## [#](https://funtl.com/zh/maven/Maven-依赖机制.html#解释说明)解释说明

看看有什么不同？那么到底在 Maven 发生了什么？当建立一个 Maven 的项目，pom.xml 文件将被解析，如果看到 log4j 的 Maven 坐标，然后 Maven 按此顺序搜索 log4j 库：

- 在 Maven 的本地仓库搜索 log4j
- 在 Maven 中央存储库搜索 log4j
- 在 Maven 远程仓库搜索 log4j(如果在 pom.xml 中定义)

Maven 依赖库管理是一个非常好的工具，为您节省了大量的工作

上次更新: 2018-12-29 1:45:15