# [#](https://funtl.com/zh/docker-compose/Docker-Compose-实战-Tomcat.html#docker-compose-实战-tomcat)Docker Compose 实战 Tomcat

```text
version: '3.1'
services:
  tomcat:
    restart: always
    image: tomcat
    container_name: tomcat
    ports:
      - 8080:8080
    volumes:
      - /usr/local/docker/tomcat/webapps/test:/usr/local/tomcat/webapps/test
    environment:
      TZ: Asia/Shanghai
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

上次更新: 2018-12-30 15:32:03

← [Docker Compose 模板文件](https://funtl.com/zh/docker-compose/Docker-Compose-模板文件.html)[Docker Compose 实战 MySQL ](https://funtl.com/zh/docker-compose/Docker-Compose-实战-MySQL.html)→