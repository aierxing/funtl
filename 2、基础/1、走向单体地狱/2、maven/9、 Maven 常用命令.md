# Maven 常用命令

本章节只提供 Maven 使用时的一些基本命令

## [#](https://funtl.com/zh/maven/Maven-常用命令.html#清除产生的项目)清除产生的项目

```shell
mvn clean
```

## [#](https://funtl.com/zh/maven/Maven-常用命令.html#编译源代码)编译源代码

```shell
mvn compile
```

## [#](https://funtl.com/zh/maven/Maven-常用命令.html#打包)打包

```shell
mvn package
```

## [#](https://funtl.com/zh/maven/Maven-常用命令.html#只打包不测试（跳过测试）)只打包不测试（跳过测试）

```text
mvn -dmaven.test.skip=true
```

## [#](https://funtl.com/zh/maven/Maven-常用命令.html#安装到本地仓库)安装到本地仓库

```text
mvn install
```

## [#](https://funtl.com/zh/maven/Maven-常用命令.html#源码打包)源码打包

```text
mvn source:jar
或
mvn source:jar-no-fork
```

上次更新: 2018-12-29 1:45:15