# [#](https://funtl.com/zh/docker/Docker-常用命令.html#docker-常用命令)Docker 常用命令

## [#](https://funtl.com/zh/docker/Docker-常用命令.html#查看-docker-版本)查看 Docker 版本

```text
docker version
```

## [#](https://funtl.com/zh/docker/Docker-常用命令.html#从-docker-文件构建-docker-映像)从 Docker 文件构建 Docker 映像

```text
docker build -t image-name docker-file-location
```

## [#](https://funtl.com/zh/docker/Docker-常用命令.html#运行-docker-映像)运行 Docker 映像

```text
docker run -d image-name
```

## [#](https://funtl.com/zh/docker/Docker-常用命令.html#查看可用的-docker-映像)查看可用的 Docker 映像

```text
docker images
```

## [#](https://funtl.com/zh/docker/Docker-常用命令.html#查看最近的运行容器)查看最近的运行容器

```text
docker ps -l
```

## [#](https://funtl.com/zh/docker/Docker-常用命令.html#查看所有正在运行的容器)查看所有正在运行的容器

```text
docker ps -a
```

## [#](https://funtl.com/zh/docker/Docker-常用命令.html#停止运行容器)停止运行容器

```text
docker stop container_id
```

## [#](https://funtl.com/zh/docker/Docker-常用命令.html#删除一个镜像)删除一个镜像

```text
docker rmi image-name
```

## [#](https://funtl.com/zh/docker/Docker-常用命令.html#删除所有镜像)删除所有镜像

```text
docker rmi $(docker images -q)
```

## [#](https://funtl.com/zh/docker/Docker-常用命令.html#强制删除所有镜像)强制删除所有镜像

```text
docker rmi -r $(docker images -q)
```

## [#](https://funtl.com/zh/docker/Docker-常用命令.html#删除所有虚悬镜像)删除所有虚悬镜像

```text
docker rmi $(docker images -q -f dangling=true)
```

## [#](https://funtl.com/zh/docker/Docker-常用命令.html#删除所有容器)删除所有容器

```text
docker rm $(docker ps -a -q)
```

## [#](https://funtl.com/zh/docker/Docker-常用命令.html#进入-docker-容器)进入 Docker 容器

```text
docker exec -it container-id /bin/bash
```

## [#](https://funtl.com/zh/docker/Docker-常用命令.html#查看所有数据卷)查看所有数据卷

```text
docker volume ls
```

## [#](https://funtl.com/zh/docker/Docker-常用命令.html#删除指定数据卷)删除指定数据卷

```text
docker volume rm [volume_name]
```

## [#](https://funtl.com/zh/docker/Docker-常用命令.html#删除所有未关联的数据卷)删除所有未关联的数据卷

```text
docker volume rm $(docker volume ls -qf dangling=true)
```

## [#](https://funtl.com/zh/docker/Docker-常用命令.html#从主机复制文件到容器)从主机复制文件到容器

```text
sudo docker cp host_path containerID:container_path
```

## [#](https://funtl.com/zh/docker/Docker-常用命令.html#从容器复制文件到主机)从容器复制文件到主机

```text
sudo docker cp containerID:container_path host_path
```

上次更新: 2018-12-30 15:32:03

← [部署项目到容器](https://funtl.com/zh/docker/部署项目到容器.html)