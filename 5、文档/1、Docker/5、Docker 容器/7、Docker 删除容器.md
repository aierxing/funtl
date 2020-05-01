# [#](https://funtl.com/zh/docs-docker/Docker-删除容器.html#docker-删除容器)Docker 删除容器

可以使用 `docker container rm` 来删除一个处于终止状态的容器。例如

```bash
$ docker container rm  trusting_newton
trusting_newton
```

1
2

如果要删除一个运行中的容器，可以添加 `-f` 参数。Docker 会发送 `SIGKILL` 信号给容器。

## [#](https://funtl.com/zh/docs-docker/Docker-删除容器.html#清理所有处于终止状态的容器)清理所有处于终止状态的容器

用 `docker container ls -a` 命令可以查看所有已经创建的包括终止状态的容器，如果数量太多要一个个删除可能会很麻烦，用下面的命令可以清理掉所有处于终止状态的容器。

```bash
$ docker container prune
```

1

上次更新: 2019-1-3 2:03:12

← [Docker 导出和导入容器](https://funtl.com/zh/docs-docker/Docker-导出和导入容器.html)[访问 Docker 仓库 ](https://funtl.com/zh/docs-docker/Docker-访问-Docker-仓库.html)→