# [#](https://funtl.com/zh/docs-docker/编辑网络配置文件.html#docker-编辑网络配置文件)Docker 编辑网络配置文件

Docker 1.2.0 开始支持在运行中的容器里编辑 `/etc/hosts`, `/etc/hostname` 和 `/etc/resolv.conf` 文件。

但是这些修改是临时的，只在运行的容器中保留，容器终止或重启后并不会被保存下来。也不会被 `docker commit` 提交。

上次更新: 2019-7-8 4:56:52

← [Docker 工具和示例](https://funtl.com/zh/docs-docker/工具和示例.html)[实例：创建一个点到点连接 ](https://funtl.com/zh/docs-docker/实例：创建一个点到点连接.html)→