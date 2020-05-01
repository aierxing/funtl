# [#](https://funtl.com/zh/docs-docker/Docker-容器互联.html#docker-容器互联)Docker 容器互联

如果你之前有 `Docker` 使用经验，你可能已经习惯了使用 `--link` 参数来使容器互联。

随着 Docker 网络的完善，强烈建议大家将容器加入自定义的 Docker 网络来连接多个容器，而不是使用 `--link` 参数。

## [#](https://funtl.com/zh/docs-docker/Docker-容器互联.html#新建网络)新建网络

下面先创建一个新的 Docker 网络。

```bash
$ docker network create -d bridge my-net
```

1

`-d` 参数指定 Docker 网络类型，有 `bridge` `overlay`。其中 `overlay` 网络类型用于 `Swarm mode`，在本小节中你可以忽略它。

## [#](https://funtl.com/zh/docs-docker/Docker-容器互联.html#连接容器)连接容器

运行一个容器并连接到新建的 `my-net` 网络

```bash
$ docker run -it --rm --name busybox1 --network my-net busybox sh
```

1

打开新的终端，再运行一个容器并加入到 `my-net` 网络

```bash
$ docker run -it --rm --name busybox2 --network my-net busybox sh
```

1

再打开一个新的终端查看容器信息

```bash
$ docker container ls

CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
b47060aca56b        busybox             "sh"                11 minutes ago      Up 11 minutes                           busybox2
8720575823ec        busybox             "sh"                16 minutes ago      Up 16 minutes                           busybox1
```

1
2
3
4
5

下面通过 `ping` 来证明 `busybox1` 容器和 `busybox2` 容器建立了互联关系。

在 `busybox1` 容器输入以下命令

```bash
/ # ping busybox2
PING busybox2 (172.19.0.3): 56 data bytes
64 bytes from 172.19.0.3: seq=0 ttl=64 time=0.072 ms
64 bytes from 172.19.0.3: seq=1 ttl=64 time=0.118 ms
```

1
2
3
4

用 ping 来测试连接 `busybox2` 容器，它会解析成 `172.19.0.3`。

同理在 `busybox2` 容器执行 `ping busybox1`，也会成功连接到。

```bash
/ # ping busybox1
PING busybox1 (172.19.0.2): 56 data bytes
64 bytes from 172.19.0.2: seq=0 ttl=64 time=0.064 ms
64 bytes from 172.19.0.2: seq=1 ttl=64 time=0.143 ms
```

1
2
3
4

这样，`busybox1` 容器和 `busybox2` 容器建立了互联关系。

## [#](https://funtl.com/zh/docs-docker/Docker-容器互联.html#docker-compose)Docker Compose

如果你有多个容器之间需要互相连接，推荐使用 `Docker Compose`。

上次更新: 2019-1-3 2:03:12

← [Docker 外部访问容器](https://funtl.com/zh/docs-docker/Docker-外部访问容器.html)[Docker 配置 DNS ](https://funtl.com/zh/docs-docker/Docker-配置-DNS.html)→