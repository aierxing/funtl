# [#](https://funtl.com/zh/linux/Linux-安装-Tomcat.html#linux-安装-tomcat)Linux 安装 Tomcat

## [#](https://funtl.com/zh/linux/Linux-安装-Tomcat.html#本节视频)本节视频

- [【视频】基础设施即服务-Linux-安装 Tomcat](https://www.bilibili.com/video/av27165652/)

## [#](https://funtl.com/zh/linux/Linux-安装-Tomcat.html#概述)概述

此处以 Tomcat 8.5.23 为例

## [#](https://funtl.com/zh/linux/Linux-安装-Tomcat.html#下载地址)下载地址

https://tomcat.apache.org/

## [#](https://funtl.com/zh/linux/Linux-安装-Tomcat.html#解压缩并移动到指定目录)解压缩并移动到指定目录

### [#](https://funtl.com/zh/linux/Linux-安装-Tomcat.html#解压缩)解压缩

```text
tar -zxvf apache-tomcat-8.5.23.tar.gz
```

### [#](https://funtl.com/zh/linux/Linux-安装-Tomcat.html#变更目录名)变更目录名

```text
mv apache-tomcat-8.5.23 tomcat
```

### [#](https://funtl.com/zh/linux/Linux-安装-Tomcat.html#移动目录)移动目录

```text
mv tomcat/ /usr/local/
```

## [#](https://funtl.com/zh/linux/Linux-安装-Tomcat.html#常用命令)常用命令

### [#](https://funtl.com/zh/linux/Linux-安装-Tomcat.html#启动)启动

```text
/usr/local/tomcat/bin/startup.sh
```

### [#](https://funtl.com/zh/linux/Linux-安装-Tomcat.html#停止)停止

```text
/usr/local/tomcat/bin/shutdown.sh
```

### [#](https://funtl.com/zh/linux/Linux-安装-Tomcat.html#目录内执行脚本)目录内执行脚本

```text
./startup.sh
```

上次更新: 2018-12-30 15:32:03

← [Linux 安装 Java](https://funtl.com/zh/linux/Linux-安装-Java.html)