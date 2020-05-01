# [#](https://funtl.com/zh/docs-docker/Docker-访问-Docker-仓库.html#访问-docker-仓库)访问 Docker 仓库

仓库（`Repository`）是集中存放镜像的地方。

一个容易混淆的概念是注册服务器（`Registry`）。实际上注册服务器是管理仓库的具体服务器，每个服务器上可以有多个仓库，而每个仓库下面有多个镜像。从这方面来说，仓库可以被认为是一个具体的项目或目录。例如对于仓库地址 `dl.dockerpool.com/ubuntu` 来说，`dl.dockerpool.com` 是注册服务器地址，`ubuntu` 是仓库名。

大部分时候，并不需要严格区分这两者的概念。

上次更新: 2019-1-3 2:03:12

← [Docker 删除容器](https://funtl.com/zh/docs-docker/Docker-删除容器.html)[Docker Hub ](https://funtl.com/zh/docs-docker/Docker-Docker-Hub.html)→