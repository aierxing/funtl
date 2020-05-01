# 在项目中使用 Maven 私服

## [#](https://funtl.com/zh/nexus/在项目中使用-Maven-私服.html#本节视频)本节视频

- [【视频】平台即服务-Nexus-在项目中使用 Maven 私服](https://www.bilibili.com/video/av27624534)

## [#](https://funtl.com/zh/nexus/在项目中使用-Maven-私服.html#配置认证信息)配置认证信息

在 Maven `settings.xml` 中添加 Nexus 认证信息(`servers` 节点下)：

```text
<server>
  <id>nexus-releases</id>
  <username>admin</username>
  <password>admin123</password>
</server>

<server>
  <id>nexus-snapshots</id>
  <username>admin</username>
  <password>admin123</password>
</server>
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

### [#](https://funtl.com/zh/nexus/在项目中使用-Maven-私服.html#snapshots-与-releases-的区别)Snapshots 与 Releases 的区别

- nexus-releases: 用于发布 Release 版本
- nexus-snapshots: 用于发布 Snapshot 版本（快照版）

Release 版本与 Snapshot 定义如下：

```text
Release: 1.0.0/1.0.0-RELEASE
Snapshot: 1.0.0-SNAPSHOT
```

1
2

- 在项目 `pom.xml` 中设置的版本号添加 `SNAPSHOT` 标识的都会发布为 `SNAPSHOT` 版本，没有 `SNAPSHOT` 标识的都会发布为 `RELEASE` 版本。
- `SNAPSHOT` 版本会自动加一个时间作为标识，如：`1.0.0-SNAPSHOT` 发布后为变成 `1.0.0-SNAPSHOT-20180522.123456-1.jar`

## [#](https://funtl.com/zh/nexus/在项目中使用-Maven-私服.html#配置自动化部署)配置自动化部署

在 `pom.xml` 中添加如下代码：

```text
<distributionManagement>  
  <repository>  
    <id>nexus-releases</id>  
    <name>Nexus Release Repository</name>  
    <url>http://127.0.0.1:8081/repository/maven-releases/</url>  
  </repository>  
  <snapshotRepository>  
    <id>nexus-snapshots</id>  
    <name>Nexus Snapshot Repository</name>  
    <url>http://127.0.0.1:8081/repository/maven-snapshots/</url>  
  </snapshotRepository>  
</distributionManagement> 
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

注意事项：

- ID 名称必须要与 `settings.xml` 中 Servers 配置的 ID 名称保持一致。
- 项目版本号中有 `SNAPSHOT` 标识的，会发布到 Nexus Snapshots Repository, 否则发布到 Nexus Release Repository，并根据 ID 去匹配授权账号。

## [#](https://funtl.com/zh/nexus/在项目中使用-Maven-私服.html#部署到仓库)部署到仓库

```text
mvn deploy
```

1

## [#](https://funtl.com/zh/nexus/在项目中使用-Maven-私服.html#上传第三方-jar-包)上传第三方 JAR 包

Nexus 3.0 不支持页面上传，可使用 maven 命令：

```text
# 如第三方JAR包：aliyun-sdk-oss-2.2.3.jar
mvn deploy:deploy-file 
  -DgroupId=com.aliyun.oss 
  -DartifactId=aliyun-sdk-oss 
  -Dversion=2.2.3 
  -Dpackaging=jar 
  -Dfile=D:\aliyun-sdk-oss-2.2.3.jar 
  -Durl=http://127.0.0.1:8081/repository/maven-3rd/ 
  -DrepositoryId=nexus-releases
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

注意事项：

- 建议在上传第三方 JAR 包时，创建单独的第三方 JAR 包管理仓库，便于管理有维护。（maven-3rd）
- `-DrepositoryId=nexus-releases` 对应的是 `settings.xml` 中 Servers 配置的 ID 名称。（授权）

## [#](https://funtl.com/zh/nexus/在项目中使用-Maven-私服.html#配置代理仓库)配置代理仓库

```text
<repositories>
    <repository>
        <id>nexus</id>
        <name>Nexus Repository</name>
        <url>http://127.0.0.1:8081/repository/maven-public/</url>
        <snapshots>
            <enabled>true</enabled>
        </snapshots>
        <releases>
            <enabled>true</enabled>
        </releases>
    </repository>
</repositories>
<pluginRepositories>
    <pluginRepository>
        <id>nexus</id>
        <name>Nexus Plugin Repository</name>
        <url>http://127.0.0.1:8081/repository/maven-public/</url>
        <snapshots>
            <enabled>true</enabled>
        </snapshots>
        <releases>
            <enabled>true</enabled>
        </releases>
    </pluginRepository>
</pluginRepositories>
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

上次更新: 2018-12-30 15:32:03

← [Maven 仓库介绍](https://funtl.com/zh/nexus/Maven-仓库介绍.html)