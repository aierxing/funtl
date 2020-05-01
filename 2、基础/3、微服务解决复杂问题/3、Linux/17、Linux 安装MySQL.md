# [#](https://funtl.com/zh/linux/Linux-安装-MySQL.html#linux-安装-mysql)Linux 安装 MySQL

## [#](https://funtl.com/zh/linux/Linux-安装-MySQL.html#本节视频)本节视频

- [【视频】基础设施即服务-Linux-安装 MySQL](https://www.bilibili.com/video/av27165678/)

## [#](https://funtl.com/zh/linux/Linux-安装-MySQL.html#安装)安装

### [#](https://funtl.com/zh/linux/Linux-安装-MySQL.html#更新数据源)更新数据源

```text
apt-get update
```

### [#](https://funtl.com/zh/linux/Linux-安装-MySQL.html#安装-mysql)安装 MySQL

```text
apt-get install mysql-server
```

系统将提示您在安装过程中创建 root 密码。选择一个安全的密码，并确保你记住它，因为你以后需要它。接下来，我们将完成 MySQL 的配置。

## [#](https://funtl.com/zh/linux/Linux-安装-MySQL.html#配置)配置

因为是全新安装，您需要运行附带的安全脚本。这会更改一些不太安全的默认选项，例如远程 root 登录和示例用户。在旧版本的 MySQL 上，您需要手动初始化数据目录，但 Mysql 5.7 已经自动完成了。

运行安全脚本：

```text
mysql_secure_installation
```

这将提示您输入您在之前步骤中创建的 root 密码。您可以按 Y，然后 ENTER 接受所有后续问题的默认值，但是要询问您是否要更改 root 密码。您只需在之前步骤中进行设置即可，因此无需现在更改。

## [#](https://funtl.com/zh/linux/Linux-安装-MySQL.html#测试)测试

按上边方式安装完成后，MySQL 应该已经开始自动运行了。要测试它，请检查其状态。

```text
lusifer@ubuntu:~$ systemctl status mysql.service
● mysql.service - MySQL Community Server
   Loaded: loaded (/lib/systemd/system/mysql.service; enabled; vendor preset: enabled)
   Active: active (running) since Tue 2017-11-21 13:04:34 CST; 3min 24s ago
 Main PID: 2169 (mysqld)
   CGroup: /system.slice/mysql.service
           └─2169 /usr/sbin/mysqld

Nov 21 13:04:33 ubuntu systemd[1]: Starting MySQL Community Server...
Nov 21 13:04:34 ubuntu systemd[1]: Started MySQL Community Server.
```

查看 MySQL 版本：

```text
mysqladmin -p -u root version
```

## [#](https://funtl.com/zh/linux/Linux-安装-MySQL.html#配置远程访问)配置远程访问

- 修改配置文件

```text
nano /etc/mysql/mysql.conf.d/mysqld.cnf
```

- 注释掉(语句前面加上 # 即可)：

```text
bind-address = 127.0.0.1
```

- 重启 MySQL

```text
service mysql restart
```

- 登录 MySQL

```text
mysql -u root -p
```

- 授权 root 用户允许所有人连接

```text
grant all privileges on *.* to 'root'@'%' identified by '你的 mysql root 账户密码';
```

### [#](https://funtl.com/zh/linux/Linux-安装-MySQL.html#因弱口令无法成功授权解决步骤)因弱口令无法成功授权解决步骤

- 查看和设置密码安全级别

```text
select @@validate_password_policy;
```

```text
set global validate_password_policy=0;
```

- 查看和设置密码长度限制

```text
select @@validate_password_length;
```

```text
set global validate_password_length=1;
```

## [#](https://funtl.com/zh/linux/Linux-安装-MySQL.html#常用命令)常用命令

### [#](https://funtl.com/zh/linux/Linux-安装-MySQL.html#启动)启动

```text
service mysql start
```

### [#](https://funtl.com/zh/linux/Linux-安装-MySQL.html#停止)停止

```text
service mysql stop
```

### [#](https://funtl.com/zh/linux/Linux-安装-MySQL.html#重启)重启

```text
service mysql restart
```

## [#](https://funtl.com/zh/linux/Linux-安装-MySQL.html#其它配置)其它配置

修改配置 `mysqld.cnf` 配置文件

```text
vi /etc/mysql/mysql.conf.d/mysqld.cnf
```

### [#](https://funtl.com/zh/linux/Linux-安装-MySQL.html#配置默认字符集)配置默认字符集

在 `[mysqld]` 节点上增加如下配置

```text
[client]
default-character-set=utf8
```

在 `[mysqld]` 节点底部增加如下配置

```text
default-storage-engine=INNODB
character-set-server=utf8
collation-server=utf8_general_ci
```

### [#](https://funtl.com/zh/linux/Linux-安装-MySQL.html#配置忽略数据库大小写敏感)配置忽略数据库大小写敏感

在 `[mysqld]` 节点底部增加如下配置

```text
lower-case-table-names = 1
```

上次更新: 2018-12-30 15:32:03

← [Linux 安装 Tomcat](https://funtl.com/zh/linux/Linux-安装-Tomcat.html)