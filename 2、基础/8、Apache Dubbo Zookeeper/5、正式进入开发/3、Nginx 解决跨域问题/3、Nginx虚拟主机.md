# [#](https://funtl.com/zh/apache-dubbo-codeing/Nginx-虚拟主机.html#nginx-虚拟主机)Nginx 虚拟主机

## [#](https://funtl.com/zh/apache-dubbo-codeing/Nginx-虚拟主机.html#本节视频)本节视频

- [【视频】Dubbo 实现微服务架构-使用 Nginx 解决跨域问题-Nginx 虚拟主机](https://www.bilibili.com/video/av35251755/)

## [#](https://funtl.com/zh/apache-dubbo-codeing/Nginx-虚拟主机.html#概述)概述

我们使用 Docker 来安装和运行 Nginx，`docker-compose.yml` 配置如下：

```text
version: '3.1'
services:
  nginx:
    restart: always
    image: nginx
    container_name: nginx
    ports:
      - 81:80
    volumes:
      - ./conf/nginx.conf:/etc/nginx/nginx.conf
      - ./wwwroot:/usr/share/nginx/wwwroot
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

## [#](https://funtl.com/zh/apache-dubbo-codeing/Nginx-虚拟主机.html#什么是虚拟主机？)什么是虚拟主机？

虚拟主机是一种特殊的软硬件技术，它可以将网络上的每一台计算机分成多个虚拟主机，每个虚拟主机可以独立对外提供 www 服务，这样就可以实现一台主机对外提供多个 web 服务，每个虚拟主机之间是独立的，互不影响的。

通过 Nginx 可以实现虚拟主机的配置，Nginx 支持三种类型的虚拟主机配置

- 基于 IP 的虚拟主机
- 基于域名的虚拟主机
- 基于端口的虚拟主机

## [#](https://funtl.com/zh/apache-dubbo-codeing/Nginx-虚拟主机.html#nginx-配置文件的结构)Nginx 配置文件的结构

```text
# ...
events {
	# ...
}

http {
	# ...
	server{
		# ...
	}
	
	# ...
	server{
		# ...
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

**注：** 每个 server 就是一个虚拟主机

## [#](https://funtl.com/zh/apache-dubbo-codeing/Nginx-虚拟主机.html#基于端口的虚拟主机配置)基于端口的虚拟主机配置

### [#](https://funtl.com/zh/apache-dubbo-codeing/Nginx-虚拟主机.html#需求)需求

- Nginx 对外提供 80 和 8080 两个端口监听服务
- 请求 80 端口则请求 html80 目录下的 html
- 请求 8080 端口则请求 html8080 目录下的 html

### [#](https://funtl.com/zh/apache-dubbo-codeing/Nginx-虚拟主机.html#创建目录及文件)创建目录及文件

在 `/usr/local/docker/nginx/wwwroot` 目录下创建 `html80` 和 `html8080` 两个目录，并分辨创建两个 index.html 文件

### [#](https://funtl.com/zh/apache-dubbo-codeing/Nginx-虚拟主机.html#配置虚拟主机)配置虚拟主机

修改 `/usr/local/docker/nginx/conf` 目录下的 nginx.conf 配置文件：

```text
# 启动进程,通常设置成和 CPU 的数量相等
worker_processes  1;

events {
    # epoll 是多路复用 IO(I/O Multiplexing) 中的一种方式
    # 但是仅用于 linux2.6 以上内核,可以大大提高 nginx 的性能
    use epoll;
    # 单个后台 worker process 进程的最大并发链接数
    worker_connections  1024;
}

http {
    # 设定 mime 类型,类型由 mime.type 文件定义
    include       mime.types;
    default_type  application/octet-stream;

    # sendfile 指令指定 nginx 是否调用 sendfile 函数（zero copy 方式）来输出文件，对于普通应用，
    # 必须设为 on，如果用来进行下载等应用磁盘 IO 重负载应用，可设置为 off，以平衡磁盘与网络 I/O 处理速度，降低系统的 uptime.
    sendfile        on;
    
    # 连接超时时间
    keepalive_timeout  65;
    # 设定请求缓冲
    client_header_buffer_size 2k;

    # 配置虚拟主机 192.168.75.145
    server {
	# 监听的ip和端口，配置 192.168.75.145:80
        listen       80;
	# 虚拟主机名称这里配置ip地址
        server_name  192.168.75.145;
	# 所有的请求都以 / 开始，所有的请求都可以匹配此 location
        location / {
	    # 使用 root 指令指定虚拟主机目录即网页存放目录
	    # 比如访问 http://ip/index.html 将找到 /usr/local/docker/nginx/wwwroot/html80/index.html
	    # 比如访问 http://ip/item/index.html 将找到 /usr/local/docker/nginx/wwwroot/html80/item/index.html

            root   /usr/share/nginx/wwwroot/html80;
	    # 指定欢迎页面，按从左到右顺序查找
            index  index.html index.htm;
        }

    }
    # 配置虚拟主机 192.168.75.245
    server {
        listen       8080;
        server_name  192.168.75.145;

        location / {
            root   /usr/share/nginx/wwwroot/html8080;
            index  index.html index.htm;
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

## [#](https://funtl.com/zh/apache-dubbo-codeing/Nginx-虚拟主机.html#基于域名的虚拟主机配置)基于域名的虚拟主机配置

### [#](https://funtl.com/zh/apache-dubbo-codeing/Nginx-虚拟主机.html#需求-2)需求

- 两个域名指向同一台 Nginx 服务器，用户访问不同的域名显示不同的网页内容
- 两个域名是 admin.service.itoken.funtl.com 和 admin.web.itoken.funtl.com
- Nginx 服务器使用虚拟机 192.168.75.145

### [#](https://funtl.com/zh/apache-dubbo-codeing/Nginx-虚拟主机.html#配置-windows-hosts-文件)配置 Windows Hosts 文件

- 通过 host 文件指定 admin.service.itoken.funtl.com 和 admin.web.itoken.funtl.com 对应 192.168.75.145 虚拟机：
- 修改 window 的 hosts 文件：（C:\Windows\System32\drivers\etc）

![img](https://funtl.com/assets/Lusifer1520965332.png)

### [#](https://funtl.com/zh/apache-dubbo-codeing/Nginx-虚拟主机.html#创建目录及文件-2)创建目录及文件

在 `/usr/local/docker/nginx/wwwroot` 目录下创建 `htmlservice` 和 `htmlweb` 两个目录，并分辨创建两个 index.html 文件

### [#](https://funtl.com/zh/apache-dubbo-codeing/Nginx-虚拟主机.html#配置虚拟主机-2)配置虚拟主机

```text
user  nginx;
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
        listen       80;
        server_name  admin.service.itoken.funtl.com;
        location / {
            root   /usr/share/nginx/wwwroot/htmlservice;
            index  index.html index.htm;
        }

    }

    server {
        listen       80;
        server_name  admin.web.itoken.funtl.com;

        location / {
            root   /usr/share/nginx/wwwroot/htmlweb;
            index  index.html index.htm;
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
30
31
32
33
34

上次更新: 2019-4-19 19:23:15

← [什么是 Nginx](https://funtl.com/zh/apache-dubbo-codeing/什么是-Nginx.html)[Nginx 反向代理 ](https://funtl.com/zh/apache-dubbo-codeing/Nginx-反向代理.html)→