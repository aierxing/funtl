# [#](https://funtl.com/zh/spring-cloud-itoken-codeing/Nginx-反向代理.html#nginx-反向代理)Nginx 反向代理

## [#](https://funtl.com/zh/spring-cloud-itoken-codeing/Nginx-反向代理.html#本节视频)本节视频

- [【视频】项目实战-iToken-反向代理负载均衡-Nginx 反向代理](https://www.bilibili.com/video/av28695708)

## [#](https://funtl.com/zh/spring-cloud-itoken-codeing/Nginx-反向代理.html#什么是代理服务器？)什么是代理服务器？

代理服务器，客户机在发送请求时，不会直接发送给目的主机，而是先发送给代理服务器，代理服务接受客户机请求之后，再向主机发出，并接收目的主机返回的数据，存放在代理服务器的硬盘中，再发送给客户机。

![img](https://funtl.com/assets/Lusifer2018080517010001.png)

## [#](https://funtl.com/zh/spring-cloud-itoken-codeing/Nginx-反向代理.html#为什么要使用代理服务器？)为什么要使用代理服务器？

### [#](https://funtl.com/zh/spring-cloud-itoken-codeing/Nginx-反向代理.html#提高访问速度)提高访问速度

由于目标主机返回的数据会存放在代理服务器的硬盘中，因此下一次客户再访问相同的站点数据时，会直接从代理服务器的硬盘中读取，起到了缓存的作用，尤其对于热门站点能明显提高请求速度。

### [#](https://funtl.com/zh/spring-cloud-itoken-codeing/Nginx-反向代理.html#防火墙作用)防火墙作用

由于所有的客户机请求都必须通过代理服务器访问远程站点，因此可在代理服务器上设限，过滤某些不安全信息。

### [#](https://funtl.com/zh/spring-cloud-itoken-codeing/Nginx-反向代理.html#通过代理服务器访问不能访问的目标站点)通过代理服务器访问不能访问的目标站点

互联网上有许多开放的代理服务器，客户机在访问受限时，可通过不受限的代理服务器访问目标站点，通俗说，我们使用的翻墙浏览器就是利用了代理服务器，虽然不能出国，但也可直接访问外网。

## [#](https://funtl.com/zh/spring-cloud-itoken-codeing/Nginx-反向代理.html#什么是正向代理？)什么是正向代理？

正向代理，架设在客户机与目标主机之间，只用于代理内部网络对 Internet 的连接请求，客户机必须指定代理服务器,并将本来要直接发送到 Web 服务器上的 Http 请求发送到代理服务器中。

![img](https://funtl.com/assets/Lusifer2018080517010002.png)

## [#](https://funtl.com/zh/spring-cloud-itoken-codeing/Nginx-反向代理.html#什么是反向代理？)什么是反向代理？

反向代理服务器架设在服务器端，通过缓冲经常被请求的页面来缓解服务器的工作量，将客户机请求转发给内部网络上的目标服务器；并将从服务器上得到的结果返回给 Internet 上请求连接的客户端，此时代理服务器与目标主机一起对外表现为一个服务器。

![img](https://funtl.com/assets/Lusifer2018080517010003.png)

## [#](https://funtl.com/zh/spring-cloud-itoken-codeing/Nginx-反向代理.html#反向代理有哪些主要应用？)反向代理有哪些主要应用？

现在许多大型 web 网站都用到反向代理。除了可以防止外网对内网服务器的恶性攻击、缓存以减少服务器的压力和访问安全控制之外，还可以进行负载均衡，将用户请求分配给多个服务器。

## [#](https://funtl.com/zh/spring-cloud-itoken-codeing/Nginx-反向代理.html#使用-nginx-反向代理-tomcat)使用 Nginx 反向代理 Tomcat

### [#](https://funtl.com/zh/spring-cloud-itoken-codeing/Nginx-反向代理.html#需求)需求

- 两个 tomcat 服务通过 nginx 反向代理
- nginx 服务器：192.168.75.145:80
- tomcat1 服务器：192.168.75.145:9090
- tomcat2 服务器：192.168.75.145:9091

### [#](https://funtl.com/zh/spring-cloud-itoken-codeing/Nginx-反向代理.html#启动-tomcat-容器)启动 Tomcat 容器

启动两个 Tomcat 容器，映射端口为 9090 和 9091，docker-compose.yml 如下：

```text
version: '3'
services:
  tomcat1:
    image: tomcat
    container_name: tomcat1
    ports:
      - 9090:8080

  tomcat2:
    image: tomcat
    container_name: tomcat2
    ports:
      - 9091:8080
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

### [#](https://funtl.com/zh/spring-cloud-itoken-codeing/Nginx-反向代理.html#配置-nginx-反向代理)配置 Nginx 反向代理

修改 `/usr/local/docker/nginx/conf` 目录下的 nginx.conf 配置文件：

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
	
	# 配置一个代理即 tomcat1 服务器
	upstream tomcatServer1 {
		server 192.168.75.145:9090;
	}

	# 配置一个代理即 tomcat2 服务器
	upstream tomcatServer2 {
		server 192.168.75.145:9091;
	}

	# 配置一个虚拟主机
	server {
		listen 80;
		server_name admin.service.itoken.funtl.com;
		location / {
				# 域名 admin.service.itoken.funtl.com 的请求全部转发到 tomcat_server1 即 tomcat1 服务上
				proxy_pass http://tomcatServer1;
				# 欢迎页面，按照从左到右的顺序查找页面
				index index.jsp index.html index.htm;
		}
	}

	server {
		listen 80;
		server_name admin.web.itoken.funtl.com;

		location / {
			# 域名 admin.web.itoken.funtl.com 的请求全部转发到 tomcat_server2 即 tomcat2 服务上
			proxy_pass http://tomcatServer2;
			index index.jsp index.html index.htm;
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

**注意：新版 Nginx 的 `upstream` 配置中的名称不可以有下划线("_")，否则会报 `400` 错误**

上次更新: 2019-4-19 19:23:15

← [小知识-Nginx 惊群问题](https://funtl.com/zh/spring-cloud-itoken-codeing/小知识-Nginx-惊群问题.html)[Nginx 负载均衡 ](https://funtl.com/zh/spring-cloud-itoken-codeing/Nginx-负载均衡.html)→