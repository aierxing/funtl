# [#](https://funtl.com/zh/linux/Linux-软件包管理.html#linux-软件包管理)Linux 软件包管理

## [#](https://funtl.com/zh/linux/Linux-软件包管理.html#本节视频)本节视频

- [【视频】基础设施即服务-Linux-软件的安装与卸载](https://www.bilibili.com/video/av27165621/)

## [#](https://funtl.com/zh/linux/Linux-软件包管理.html#概述)概述

APT(Advanced Packaging Tool) 是 Debian/Ubuntu 类 Linux 系统中的软件包管理程序, 使用它可以找到想要的软件包, 而且安装、卸载、更新都很简便；也可以用来对 Ubuntu 进行升级; APT 的源文件为 `/etc/apt/` 目录下的 `sources.list` 文件。

## [#](https://funtl.com/zh/linux/Linux-软件包管理.html#修改数据源)修改数据源

由于国内的网络环境问题，我们需要将 Ubuntu 的数据源修改为国内数据源，操作步骤如下：

### [#](https://funtl.com/zh/linux/Linux-软件包管理.html#查看系统版本)查看系统版本

```text
lsb_release -a
```

输出结果为

```text
No LSB modules are available.
Distributor ID:	Ubuntu
Description:	Ubuntu 16.04 LTS
Release:	16.04
Codename:	xenial
```

**注意：** Codename 为 `xenial`，该名称为我们 Ubuntu 系统的名称，修改数据源需要用到该名称

### [#](https://funtl.com/zh/linux/Linux-软件包管理.html#编辑数据源)编辑数据源

```text
vi /etc/apt/sources.list
```

删除全部内容并修改为

```text
deb http://mirrors.aliyun.com/ubuntu/ xenial main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ xenial-security main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ xenial-updates main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ xenial-backports main restricted universe multiverse
```

### [#](https://funtl.com/zh/linux/Linux-软件包管理.html#更新数据源)更新数据源

```text
apt-get update
```

## [#](https://funtl.com/zh/linux/Linux-软件包管理.html#常用-apt-命令)常用 APT 命令

### [#](https://funtl.com/zh/linux/Linux-软件包管理.html#安装软件包)安装软件包

```text
apt-get install packagename
```

### [#](https://funtl.com/zh/linux/Linux-软件包管理.html#删除软件包)删除软件包

```text
apt-get remove packagename
```

### [#](https://funtl.com/zh/linux/Linux-软件包管理.html#更新软件包列表)更新软件包列表

```text
apt-get update
```

### [#](https://funtl.com/zh/linux/Linux-软件包管理.html#升级有可用更新的系统（慎用）)升级有可用更新的系统（慎用）

```text
apt-get upgrade
```

## [#](https://funtl.com/zh/linux/Linux-软件包管理.html#其它-apt-命令)其它 APT 命令

### [#](https://funtl.com/zh/linux/Linux-软件包管理.html#搜索)搜索

```text
apt-cache search package
```

### [#](https://funtl.com/zh/linux/Linux-软件包管理.html#获取包信息)获取包信息

```text
apt-cache show package
```

### [#](https://funtl.com/zh/linux/Linux-软件包管理.html#删除包及配置文件)删除包及配置文件

```text
apt-get remove package --purge
```

### [#](https://funtl.com/zh/linux/Linux-软件包管理.html#了解使用依赖)了解使用依赖

```text
apt-cache depends package
```

### [#](https://funtl.com/zh/linux/Linux-软件包管理.html#查看被哪些包依赖)查看被哪些包依赖

```text
apt-cache rdepends package
```

### [#](https://funtl.com/zh/linux/Linux-软件包管理.html#安装相关的编译环境)安装相关的编译环境

```text
apt-get build-dep package
```

### [#](https://funtl.com/zh/linux/Linux-软件包管理.html#下载源代码)下载源代码

```text
apt-get source package
```

### [#](https://funtl.com/zh/linux/Linux-软件包管理.html#清理无用的包)清理无用的包

```text
apt-get clean && apt-get autoclean
```

### [#](https://funtl.com/zh/linux/Linux-软件包管理.html#检查是否有损坏的依赖)检查是否有损坏的依赖

```text
apt-get check
```

上次更新: 2018-12-30 15:32:03

← [Linux 编辑器](https://funtl.com/zh/linux/Linux-编辑器.html)