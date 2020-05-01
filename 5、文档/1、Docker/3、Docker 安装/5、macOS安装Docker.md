# [#](https://funtl.com/zh/docs-docker/macOS-安装-Docker.html#macos-安装-docker)macOS 安装 Docker

## [#](https://funtl.com/zh/docs-docker/macOS-安装-Docker.html#系统要求)系统要求

[Docker for Mac](https://docs.docker.com/docker-for-mac/) 要求系统最低为 macOS 10.10.3 Yosemite。如果系统不满足需求，可以安装 [Docker Toolbox](https://docs.docker.com/toolbox/overview/)。

## [#](https://funtl.com/zh/docs-docker/macOS-安装-Docker.html#安装)安装

### [#](https://funtl.com/zh/docs-docker/macOS-安装-Docker.html#使用-homebrew-安装)使用 Homebrew 安装

[Homebrew](http://brew.sh/) 的 [Cask](https://caskroom.github.io/) 已经支持 Docker for Mac，因此可以很方便的使用 Homebrew Cask 来进行安装：

```bash
$ brew cask install docker
```

1

### [#](https://funtl.com/zh/docs-docker/macOS-安装-Docker.html#手动下载安装)手动下载安装

如果需要手动下载，请点击以下链接下载 [Stable](https://download.docker.com/mac/stable/Docker.dmg) 或 [Edge](https://download.docker.com/mac/edge/Docker.dmg) 版本的 Docker for Mac。

如同 macOS 其它软件一样，安装也非常简单，双击下载的 `.dmg` 文件，然后将那只叫 [Moby](https://blog.docker.com/2013/10/call-me-moby-dock/) 的鲸鱼图标拖拽到 `Application` 文件夹即可（其间需要输入用户密码）。

![img](https://funtl.com/assets/install-mac-dmg.png)

## [#](https://funtl.com/zh/docs-docker/macOS-安装-Docker.html#运行)运行

从应用中找到 Docker 图标并点击运行。

![img](https://funtl.com/assets/install-mac-apps.png)

运行之后，会在右上角菜单栏看到多了一个鲸鱼图标，这个图标表明了 Docker 的运行状态。

![img](https://funtl.com/assets/install-mac-menubar.png)

第一次点击图标，可能会看到这个安装成功的界面，点击 "Got it!" 可以关闭这个窗口。

![img](https://funtl.com/assets/install-mac-success.png)

以后每次点击鲸鱼图标会弹出操作菜单。

![img](https://funtl.com/assets/install-mac-menu.png)

启动终端后，通过命令可以检查安装后的 Docker 版本。

```bash
$ docker --version
Docker version 17.10.0-ce, build f4ffd25
$ docker-compose --version
docker-compose version 1.17.0-rc1, build a0f95af
$ docker-machine --version
docker-machine version 0.13.0, build 9ba6da9
```

1
2
3
4
5
6

如果 `docker version`、`docker info` 都正常的话，可以尝试运行一个 [Nginx 服务器](https://store.docker.com/images/nginx/)：

```bash
$ docker run -d -p 80:80 --name webserver nginx
```

1

服务运行后，可以访问 [http://localhost](http://localhost/)，如果看到了 "Welcome to nginx!"，就说明 Docker for Mac 安装成功了。

![img](https://funtl.com/assets/install-mac-example-nginx.png)

要停止 Nginx 服务器并删除执行下面的命令：

```bash
$ docker stop webserver
$ docker rm webserver
```

1
2

## [#](https://funtl.com/zh/docs-docker/macOS-安装-Docker.html#镜像加速)镜像加速

鉴于国内网络问题，后续拉取 Docker 镜像十分缓慢，强烈建议安装 Docker 之后配置 `国内镜像加速`。

上次更新: 2019-4-19 19:23:15

← [树莓派卡片电脑安装 Docker](https://funtl.com/zh/docs-docker/树莓派安装-Docker.html)[Windows 安装 Docker ](https://funtl.com/zh/docs-docker/Windows-安装-Docker.html)→