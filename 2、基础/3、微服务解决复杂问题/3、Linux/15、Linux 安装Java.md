# [#](https://funtl.com/zh/linux/Linux-安装-Java.html#linux-安装-java)Linux 安装 Java

## [#](https://funtl.com/zh/linux/Linux-安装-Java.html#本节视频)本节视频

- [【视频】基础设施即服务-Linux-安装 Java](https://www.bilibili.com/video/av27165645/)

## [#](https://funtl.com/zh/linux/Linux-安装-Java.html#概述)概述

此处以 JDK 1.8.0_152 为例

## [#](https://funtl.com/zh/linux/Linux-安装-Java.html#下载地址)下载地址

http://www.oracle.com/technetwork/java/javase/downloads/index.html

## [#](https://funtl.com/zh/linux/Linux-安装-Java.html#解压缩并移动到指定目录)解压缩并移动到指定目录

### [#](https://funtl.com/zh/linux/Linux-安装-Java.html#解压缩)解压缩

```text
tar -zxvf jdk-8u152-linux-x64.tar.gz
```

### [#](https://funtl.com/zh/linux/Linux-安装-Java.html#创建目录)创建目录

```text
mkdir -p /usr/local/java
```

### [#](https://funtl.com/zh/linux/Linux-安装-Java.html#移动安装包)移动安装包

```text
mv jdk1.8.0_152/ /usr/local/java/
```

### [#](https://funtl.com/zh/linux/Linux-安装-Java.html#设置所有者)设置所有者

```text
chown -R root:root /usr/local/java/
```

## [#](https://funtl.com/zh/linux/Linux-安装-Java.html#配置环境变量)配置环境变量

### [#](https://funtl.com/zh/linux/Linux-安装-Java.html#配置系统环境变量)配置系统环境变量

```text
nano /etc/environment
```

### [#](https://funtl.com/zh/linux/Linux-安装-Java.html#添加如下语句)添加如下语句

```text
PATH="/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games"
export JAVA_HOME=/usr/local/java/jdk1.8.0_152
export JRE_HOME=/usr/local/java/jdk1.8.0_152/jre
export CLASSPATH=$CLASSPATH:$JAVA_HOME/lib:$JAVA_HOME/jre/lib
```

### [#](https://funtl.com/zh/linux/Linux-安装-Java.html#配置用户环境变量)配置用户环境变量

```text
nano /etc/profile
```

### [#](https://funtl.com/zh/linux/Linux-安装-Java.html#添加如下语句-2)添加如下语句

```text
if [ "$PS1" ]; then
  if [ "$BASH" ] && [ "$BASH" != "/bin/sh" ]; then
    # The file bash.bashrc already sets the default PS1.
    # PS1='\h:\w\$ '
    if [ -f /etc/bash.bashrc ]; then
      . /etc/bash.bashrc
    fi
  else
    if [ "`id -u`" -eq 0 ]; then
      PS1='# '
    else
      PS1='$ '
    fi
  fi
fi

export JAVA_HOME=/usr/local/java/jdk1.8.0_152
export JRE_HOME=/usr/local/java/jdk1.8.0_152/jre
export CLASSPATH=$CLASSPATH:$JAVA_HOME/lib:$JAVA_HOME/jre/lib
export PATH=$JAVA_HOME/bin:$JAVA_HOME/jre/bin:$PATH:$HOME/bin

if [ -d /etc/profile.d ]; then
  for i in /etc/profile.d/*.sh; do
    if [ -r $i ]; then
      . $i
    fi
  done
  unset i
fi
```

### [#](https://funtl.com/zh/linux/Linux-安装-Java.html#使用户环境变量生效)使用户环境变量生效

```text
source /etc/profile
```

## [#](https://funtl.com/zh/linux/Linux-安装-Java.html#测试是否安装成功)测试是否安装成功

```text
root@UbuntuBase:/usr/local/java# java -version
java version "1.8.0_152"
Java(TM) SE Runtime Environment (build 1.8.0_152-b16)
Java HotSpot(TM) 64-Bit Server VM (build 25.152-b16, mixed mode)
```

## [#](https://funtl.com/zh/linux/Linux-安装-Java.html#为其他用户更新用户环境变量)为其他用户更新用户环境变量

```text
su lusifer
source /etc/profile
```

上次更新: 2018-12-30 15:32:03

← [Linux 文件权限管理](https://funtl.com/zh/linux/Linux-文件权限管理.html)