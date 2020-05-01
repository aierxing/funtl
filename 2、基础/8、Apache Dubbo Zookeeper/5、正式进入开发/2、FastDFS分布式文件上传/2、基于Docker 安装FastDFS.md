# [#](https://funtl.com/zh/apache-dubbo-codeing/FastDFS-安装.html#基于-docker-安装-fastdfs)基于 Docker 安装 FastDFS

## [#](https://funtl.com/zh/apache-dubbo-codeing/FastDFS-安装.html#本节视频)本节视频

- [【视频】Dubbo 实现微服务架构-分布式文件系统-基于 Docker 安装 ](https://www.bilibili.com/video/av35251669/)

## [#](https://funtl.com/zh/apache-dubbo-codeing/FastDFS-安装.html#环境准备)环境准备

**所需全部环境配置文件已上传至群中，通过首页扫码加群下载即可**

- libfastcommon.tar.gz
- fastdfs-5.11.tar.gz
- nginx-1.13.6.tar.gz
- fastdfs-nginx-module_v1.16.tar.gz

## [#](https://funtl.com/zh/apache-dubbo-codeing/FastDFS-安装.html#创建工作目录)创建工作目录

在 Linux 服务器上创建 `/usr/local/docker/fastdfs/environmen` 目录

说明：

- `/usr/local/docker/fastdfs`：用于存放 `docker-compose.yml` 配置文件及 FastDFS 的数据卷
- `/usr/local/docker/fastdfs/environmen`：用于存放 `Dockerfile` 镜像配置文件及 FastDFS 所需环境

## [#](https://funtl.com/zh/apache-dubbo-codeing/FastDFS-安装.html#docker-compose-yml)docker-compose.yml

```text
version: '3.1'
services:
  fastdfs:
    build: environment
    restart: always
    container_name: fastdfs
    volumes:
      - ./storage:/fastdfs/storage
    network_mode: host
```

1
2
3
4
5
6
7
8
9

## [#](https://funtl.com/zh/apache-dubbo-codeing/FastDFS-安装.html#dockerfile)Dockerfile

```text
FROM ubuntu:xenial
MAINTAINER topsale@vip.qq.com

# 更新数据源
WORKDIR /etc/apt
RUN echo 'deb http://mirrors.aliyun.com/ubuntu/ xenial main restricted universe multiverse' > sources.list
RUN echo 'deb http://mirrors.aliyun.com/ubuntu/ xenial-security main restricted universe multiverse' >> sources.list
RUN echo 'deb http://mirrors.aliyun.com/ubuntu/ xenial-updates main restricted universe multiverse' >> sources.list
RUN echo 'deb http://mirrors.aliyun.com/ubuntu/ xenial-backports main restricted universe multiverse' >> sources.list
RUN apt-get update

# 安装依赖
RUN apt-get install make gcc libpcre3-dev zlib1g-dev --assume-yes

# 复制工具包
ADD fastdfs-5.11.tar.gz /usr/local/src
ADD fastdfs-nginx-module_v1.16.tar.gz /usr/local/src
ADD libfastcommon.tar.gz /usr/local/src
ADD nginx-1.13.6.tar.gz /usr/local/src

# 安装 libfastcommon
WORKDIR /usr/local/src/libfastcommon
RUN ./make.sh && ./make.sh install

# 安装 FastDFS
WORKDIR /usr/local/src/fastdfs-5.11
RUN ./make.sh && ./make.sh install

# 配置 FastDFS 跟踪器
ADD tracker.conf /etc/fdfs
RUN mkdir -p /fastdfs/tracker

# 配置 FastDFS 存储
ADD storage.conf /etc/fdfs
RUN mkdir -p /fastdfs/storage

# 配置 FastDFS 客户端
ADD client.conf /etc/fdfs

# 配置 fastdfs-nginx-module
ADD config /usr/local/src/fastdfs-nginx-module/src

# FastDFS 与 Nginx 集成
WORKDIR /usr/local/src/nginx-1.13.6
RUN ./configure --add-module=/usr/local/src/fastdfs-nginx-module/src
RUN make && make install
ADD mod_fastdfs.conf /etc/fdfs

WORKDIR /usr/local/src/fastdfs-5.11/conf
RUN cp http.conf mime.types /etc/fdfs/

# 配置 Nginx
ADD nginx.conf /usr/local/nginx/conf

COPY entrypoint.sh /usr/local/bin/
ENTRYPOINT ["/usr/local/bin/entrypoint.sh"]

WORKDIR /
EXPOSE 8888
CMD ["/bin/bash"]
```

1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18
19
20
21
22
23
24
25
26
27
28
29
30
31
32
33
34
35
36
37
38
39
40
41
42
43
44
45
46
47
48
49
50
51
52
53
54
55
56
57
58
59
60

## [#](https://funtl.com/zh/apache-dubbo-codeing/FastDFS-安装.html#entrypoint-sh)entrypoint.sh

```text
#!/bin/sh
/etc/init.d/fdfs_trackerd start
/etc/init.d/fdfs_storaged start
/usr/local/nginx/sbin/nginx -g 'daemon off;'
```

1
2
3
4

注：Shell 创建后是无法直接使用的，需要赋予执行的权限，使用 `chmod +x entrypoint.sh` 命令

## [#](https://funtl.com/zh/apache-dubbo-codeing/FastDFS-安装.html#各种配置文件说明)各种配置文件说明

### [#](https://funtl.com/zh/apache-dubbo-codeing/FastDFS-安装.html#tracker-conf)tracker.conf

FastDFS 跟踪器配置，容器中路径为：/etc/fdfs，修改为：

```text
base_path=/fastdfs/tracker
```

1

### [#](https://funtl.com/zh/apache-dubbo-codeing/FastDFS-安装.html#storage-conf)storage.conf

FastDFS 存储配置，容器中路径为：/etc/fdfs，修改为：

```text
base_path=/fastdfs/storage
store_path0=/fastdfs/storage
tracker_server=192.168.75.128:22122
http.server_port=8888
```

1
2
3
4

### [#](https://funtl.com/zh/apache-dubbo-codeing/FastDFS-安装.html#client-conf)client.conf

FastDFS 客户端配置，容器中路径为：/etc/fdfs，修改为：

```text
base_path=/fastdfs/tracker
tracker_server=192.168.75.128:22122
```

1
2

### [#](https://funtl.com/zh/apache-dubbo-codeing/FastDFS-安装.html#config)config

fastdfs-nginx-module 配置文件，容器中路径为：/usr/local/src/fastdfs-nginx-module/src，修改为：

```text
# 修改前
CORE_INCS="$CORE_INCS /usr/local/include/fastdfs /usr/local/include/fastcommon/"
CORE_LIBS="$CORE_LIBS -L/usr/local/lib -lfastcommon -lfdfsclient"

# 修改后
CORE_INCS="$CORE_INCS /usr/include/fastdfs /usr/include/fastcommon/"
CORE_LIBS="$CORE_LIBS -L/usr/lib -lfastcommon -lfdfsclient"
```

1
2
3
4
5
6
7

### [#](https://funtl.com/zh/apache-dubbo-codeing/FastDFS-安装.html#mod-fastdfs-conf)mod_fastdfs.conf

fastdfs-nginx-module 配置文件，容器中路径为：/usr/local/src/fastdfs-nginx-module/src，修改为：

```text
connect_timeout=10
tracker_server=192.168.75.128:22122
url_have_group_name = true
store_path0=/fastdfs/storage
```

1
2
3
4

### [#](https://funtl.com/zh/apache-dubbo-codeing/FastDFS-安装.html#nginx-conf)nginx.conf

Nginx 配置文件，容器中路径为：/usr/local/src/nginx-1.13.6/conf，修改为：

```text
user  root;
worker_processes  1;

events {
    worker_connections  1024;
}

http {
    include       mime.types;
    default_type  application/octet-stream;

    sendfile        on;

    keepalive_timeout  65;

    server {
        listen       8888;
        server_name  localhost;

        location ~/group([0-9])/M00 {
            ngx_fastdfs_module;
        }

        error_page   500 502 503 504  /50x.html;
        location = /50x.html {
            root   html;
        }
    }
}
```

1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18
19
20
21
22
23
24
25
26
27
28
29

## [#](https://funtl.com/zh/apache-dubbo-codeing/FastDFS-安装.html#启动容器)启动容器

```text
docker-compose up -d
```

1

## [#](https://funtl.com/zh/apache-dubbo-codeing/FastDFS-安装.html#测试上传)测试上传

### [#](https://funtl.com/zh/apache-dubbo-codeing/FastDFS-安装.html#交互式进入容器)交互式进入容器

```text
docker exec -it fastdfs /bin/bash
```

1

### [#](https://funtl.com/zh/apache-dubbo-codeing/FastDFS-安装.html#测试文件上传)测试文件上传

```text
/usr/bin/fdfs_upload_file /etc/fdfs/client.conf /usr/local/src/fastdfs-5.11/INSTALL
```

1

### [#](https://funtl.com/zh/apache-dubbo-codeing/FastDFS-安装.html#服务器反馈上传地址)服务器反馈上传地址

```text
group1/M00/00/00/wKhLi1oHVMCAT2vrAAAeSwu9TgM3976771
```

1

## [#](https://funtl.com/zh/apache-dubbo-codeing/FastDFS-安装.html#测试-nginx-访问)测试 Nginx 访问

```text
http://192.168.75.128:8888/group1/M00/00/00/wKhLi1oHVMCAT2vrAAAeSwu9TgM3976771
```

1

上次更新: 2018-12-31 18:51:48

← [什么是 FastDFS](https://funtl.com/zh/apache-dubbo-codeing/FastDFS-简介.html)[配置 FastDFS Java 客户端 ](https://funtl.com/zh/apache-dubbo-codeing/配置-FastDFS-Java-客户端.html)→