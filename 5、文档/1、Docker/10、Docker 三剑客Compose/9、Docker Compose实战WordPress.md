# [#](https://funtl.com/zh/docs-docker/Docker-Compose-实战-WordPress.html#docker-compose-实战-wordpress)Docker Compose 实战 WordPress

本小节内容适合 `PHP` 开发人员阅读。

`Compose` 可以很便捷的让 `Wordpress` 运行在一个独立的环境中。

## [#](https://funtl.com/zh/docs-docker/Docker-Compose-实战-WordPress.html#创建空文件夹)创建空文件夹

假设新建一个名为 `wordpress` 的文件夹，然后进入这个文件夹。

## [#](https://funtl.com/zh/docs-docker/Docker-Compose-实战-WordPress.html#创建-docker-compose-yml-文件)创建 `docker-compose.yml` 文件

`docker-compose.yml` 文件将开启一个 `wordpress` 服务和一个独立的 `MySQL` 实例：

```yaml
version: "3"
services:

   db:
     image: mysql:5.7
     volumes:
       - db_data:/var/lib/mysql
     restart: always
     environment:
       MYSQL_ROOT_PASSWORD: somewordpress
       MYSQL_DATABASE: wordpress
       MYSQL_USER: wordpress
       MYSQL_PASSWORD: wordpress

   wordpress:
     depends_on:
       - db
     image: wordpress:latest
     ports:
       - "8000:80"
     restart: always
     environment:
       WORDPRESS_DB_HOST: db:3306
       WORDPRESS_DB_USER: wordpress
       WORDPRESS_DB_PASSWORD: wordpress
volumes:
    db_data:
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

## [#](https://funtl.com/zh/docs-docker/Docker-Compose-实战-WordPress.html#构建并运行项目)构建并运行项目

运行 `docker-compose up -d` Compose 就会拉取镜像再创建我们所需要的镜像，然后启动 `wordpress` 和数据库容器。 接着浏览器访问 `127.0.0.1:8000` 端口就能看到 `WordPress` 安装界面了。

上次更新: 2019-1-3 2:03:12

← [Docker Compose 实战 Rails](https://funtl.com/zh/docs-docker/Docker-Compose-实战-Rails.html)[什么是 Docker Machine ](https://funtl.com/zh/docs-docker/Docker-三剑客之-Machine-项目.html)→