# [#](https://funtl.com/zh/docs-docker/Docker-Machine-安装.html#docker-machine-安装)Docker Machine 安装

Docker Machine 可以在多种操作系统平台上安装，包括 Linux、macOS，以及 Windows。

## [#](https://funtl.com/zh/docs-docker/Docker-Machine-安装.html#macos、windows)macOS、Windows

Docker for Mac、Docker for Windows 自带 `docker-machine` 二进制包，安装之后即可使用。

查看版本信息。

```bash
$ docker-machine -v
docker-machine version 0.13.0, build 9ba6da9
```

1
2

## [#](https://funtl.com/zh/docs-docker/Docker-Machine-安装.html#linux)Linux

在 Linux 上的也安装十分简单，从 [官方 GitHub Release](https://github.com/docker/machine/releases) 处直接下载编译好的二进制文件即可。

例如，在 Linux 64 位系统上直接下载对应的二进制包。

```bash
$ sudo curl -L https://github.com/docker/machine/releases/download/v0.13.0/docker-machine-`uname -s`-`uname -m` > /usr/local/bin/docker-machine
$ sudo chmod +x /usr/local/bin/docker-machine
```

1
2

完成后，查看版本信息。

```bash
$ docker-machine -v
docker-machine version 0.13.0, build 9ba6da9
```

1
2

上次更新: 2019-1-3 2:03:12

← [什么是 Docker Machine](https://funtl.com/zh/docs-docker/Docker-三剑客之-Machine-项目.html)[Docker Machine 使用 ](https://funtl.com/zh/docs-docker/Docker-Machine-使用.html)→