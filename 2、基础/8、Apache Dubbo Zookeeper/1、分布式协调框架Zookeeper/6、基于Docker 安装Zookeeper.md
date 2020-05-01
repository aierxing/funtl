# [#](https://funtl.com/zh/apache-dubbo-zookeeper/基于-Docker-安装-Zookeeper.html#基于-docker-安装-zookeeper)基于 Docker 安装 Zookeeper

## [#](https://funtl.com/zh/apache-dubbo-zookeeper/基于-Docker-安装-Zookeeper.html#本节视频)本节视频

- [【视频】Dubbo 实现微服务架构-Zookeeper-基于 Docker 安装](https://www.bilibili.com/video/av34187136/)

## [#](https://funtl.com/zh/apache-dubbo-zookeeper/基于-Docker-安装-Zookeeper.html#概述)概述

Zookeeper 部署有三种方式，单机模式、集群模式、伪集群模式，以下采用 Docker 的方式部署

**注意：** 集群为大于等于3个奇数，如 3、5、7,不宜太多，集群机器多了选举和数据同步耗时长，不稳定。

## [#](https://funtl.com/zh/apache-dubbo-zookeeper/基于-Docker-安装-Zookeeper.html#单机模式)单机模式

### [#](https://funtl.com/zh/apache-dubbo-zookeeper/基于-Docker-安装-Zookeeper.html#docker-compose-yml)docker-compose.yml

```text
version: '3.1'

services:
    zoo1:
        image: zookeeper
        restart: always
        hostname: zoo1
        ports:
            - 2181:2181
        environment:
            ZOO_MY_ID: 1
            ZOO_SERVERS: server.1=zoo1:2888:3888
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

### [#](https://funtl.com/zh/apache-dubbo-zookeeper/基于-Docker-安装-Zookeeper.html#验证是否安装成功)验证是否安装成功

- 以交互的方式进入容器

```text
docker exec -it zookeeper_zoo1_1 /bin/bash
```

1

- 使用客户端连接到服务端

```text
bash-4.3# ./bin/zkCli.sh -server 192.168.75.130:2181
Connecting to 192.168.75.130:2181
2017-11-09 07:45:58,365 [myid:] - INFO  [main:Environment@100] - Client environment:zookeeper.version=3.4.10-39d3a4f269333c922ed3db283be479f9deacaa0f, built on 03/23/2017 10:13 GMT
2017-11-09 07:45:58,374 [myid:] - INFO  [main:Environment@100] - Client environment:host.name=zoo1
2017-11-09 07:45:58,374 [myid:] - INFO  [main:Environment@100] - Client environment:java.version=1.8.0_131
2017-11-09 07:45:58,380 [myid:] - INFO  [main:Environment@100] - Client environment:java.vendor=Oracle Corporation
2017-11-09 07:45:58,381 [myid:] - INFO  [main:Environment@100] - Client environment:java.home=/usr/lib/jvm/java-1.8-openjdk/jre
2017-11-09 07:45:58,381 [myid:] - INFO  [main:Environment@100] - Client environment:java.class.path=/zookeeper-3.4.10/bin/../build/classes:/zookeeper-3.4.10/bin/../build/lib/*.jar:/zookeeper-3.4.10/bin/../lib/slf4j-log4j12-1.6.1.jar:/zookeeper-3.4.10/bin/../lib/slf4j-api-1.6.1.jar:/zookeeper-3.4.10/bin/../lib/netty-3.10.5.Final.jar:/zookeeper-3.4.10/bin/../lib/log4j-1.2.16.jar:/zookeeper-3.4.10/bin/../lib/jline-0.9.94.jar:/zookeeper-3.4.10/bin/../zookeeper-3.4.10.jar:/zookeeper-3.4.10/bin/../src/java/lib/*.jar:/conf:
2017-11-09 07:45:58,381 [myid:] - INFO  [main:Environment@100] - Client environment:java.library.path=/usr/lib/jvm/java-1.8-openjdk/jre/lib/amd64/server:/usr/lib/jvm/java-1.8-openjdk/jre/lib/amd64:/usr/lib/jvm/java-1.8-openjdk/jre/../lib/amd64:/usr/java/packages/lib/amd64:/usr/lib64:/lib64:/lib:/usr/lib
2017-11-09 07:45:58,381 [myid:] - INFO  [main:Environment@100] - Client environment:java.io.tmpdir=/tmp
2017-11-09 07:45:58,381 [myid:] - INFO  [main:Environment@100] - Client environment:java.compiler=<NA>
2017-11-09 07:45:58,381 [myid:] - INFO  [main:Environment@100] - Client environment:os.name=Linux
2017-11-09 07:45:58,382 [myid:] - INFO  [main:Environment@100] - Client environment:os.arch=amd64
2017-11-09 07:45:58,382 [myid:] - INFO  [main:Environment@100] - Client environment:os.version=4.4.0-98-generic
2017-11-09 07:45:58,386 [myid:] - INFO  [main:Environment@100] - Client environment:user.name=root
2017-11-09 07:45:58,386 [myid:] - INFO  [main:Environment@100] - Client environment:user.home=/root
2017-11-09 07:45:58,386 [myid:] - INFO  [main:Environment@100] - Client environment:user.dir=/zookeeper-3.4.10
2017-11-09 07:45:58,389 [myid:] - INFO  [main:ZooKeeper@438] - Initiating client connection, connectString=192.168.75.130:2181 sessionTimeout=30000 watcher=org.apache.zookeeper.ZooKeeperMain$MyWatcher@3eb07fd3
2017-11-09 07:45:58,428 [myid:] - INFO  [main-SendThread(192.168.75.130:2181):ClientCnxn$SendThread@1032] - Opening socket connection to server 192.168.75.130/192.168.75.130:2181. Will not attempt to authenticate using SASL (unknown error)
Welcome to ZooKeeper!
JLine support is enabled
2017-11-09 07:45:58,529 [myid:] - INFO  [main-SendThread(192.168.75.130:2181):ClientCnxn$SendThread@876] - Socket connection established to 192.168.75.130/192.168.75.130:2181, initiating session
[zk: 192.168.75.130:2181(CONNECTING) 0] 2017-11-09 07:45:58,573 [myid:] - INFO  [main-SendThread(192.168.75.130:2181):ClientCnxn$SendThread@1299] - Session establishment complete on server 192.168.75.130/192.168.75.130:2181, sessionid = 0x15f9fbc12ec0000, negotiated timeout = 30000

WATCHER::

WatchedEvent state:SyncConnected type:None path:null
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

- 使用服务端工具检查服务器状态

```text
bash-4.3# ./bin/zkServer.sh status
ZooKeeper JMX enabled by default
Using config: /conf/zoo.cfg
Mode: standalone
```

1
2
3
4

## [#](https://funtl.com/zh/apache-dubbo-zookeeper/基于-Docker-安装-Zookeeper.html#集群模式)集群模式

准备 3 台 Ubuntu Server 系统，并分别配置 Zookeeper

### [#](https://funtl.com/zh/apache-dubbo-zookeeper/基于-Docker-安装-Zookeeper.html#第一台主机)第一台主机

#### [#](https://funtl.com/zh/apache-dubbo-zookeeper/基于-Docker-安装-Zookeeper.html#docker-compose-yml-2)docker-compose.yml

```text
version: '3.1'
services:
    zoo1:
        image: zookeeper
        restart: always
        environment:
            ZOO_MY_ID: 1
            ZOO_SERVERS: server.1=192.168.75.130:2888:3888 server.2=192.168.75.134:2888:3888 server.3=192.168.75.135:2888:3888
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

#### [#](https://funtl.com/zh/apache-dubbo-zookeeper/基于-Docker-安装-Zookeeper.html#验证测试)验证测试

```text
root@UbuntuBase:/usr/local/docker/zookeeper# docker exec -it zookeeper_zoo1_1 /bin/bash
bash-4.3# ./bin/zkServer.sh status
ZooKeeper JMX enabled by default
Using config: /conf/zoo.cfg
Mode: leader
```

1
2
3
4
5

### [#](https://funtl.com/zh/apache-dubbo-zookeeper/基于-Docker-安装-Zookeeper.html#第二台主机)第二台主机

#### [#](https://funtl.com/zh/apache-dubbo-zookeeper/基于-Docker-安装-Zookeeper.html#docker-compose-yml-3)docker-compose.yml

```text
version: '3.1'
services:
    zoo2:
        image: zookeeper
        restart: always
        environment:
            ZOO_MY_ID: 2
            ZOO_SERVERS: server.1=192.168.75.130:2888:3888 server.2=192.168.75.134:2888:3888 server.3=192.168.75.135:2888:3888
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

#### [#](https://funtl.com/zh/apache-dubbo-zookeeper/基于-Docker-安装-Zookeeper.html#验证测试-2)验证测试

```text
root@UbuntuBase:/usr/local/docker/zookeeper# docker exec -it zookeeper_zoo2_1 /bin/bash
bash-4.3# ./bin/zkServer.sh status
ZooKeeper JMX enabled by default
Using config: /conf/zoo.cfg
Mode: follower
```

1
2
3
4
5

### [#](https://funtl.com/zh/apache-dubbo-zookeeper/基于-Docker-安装-Zookeeper.html#第三台主机)第三台主机

#### [#](https://funtl.com/zh/apache-dubbo-zookeeper/基于-Docker-安装-Zookeeper.html#docker-compose-yml-4)docker-compose.yml

```text
version: '3.1'
services:
    zoo3:
        image: zookeeper
        restart: always
        environment:
            ZOO_MY_ID: 3
            ZOO_SERVERS: server.1=192.168.75.130:2888:3888 server.2=192.168.75.134:2888:3888 server.3=192.168.75.135:2888:3888
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

#### [#](https://funtl.com/zh/apache-dubbo-zookeeper/基于-Docker-安装-Zookeeper.html#验证测试-3)验证测试

```text
root@UbuntuBase:/usr/local/docker/zookeeper# docker exec -it zookeeper_zoo3_1 /bin/bash
bash-4.3# ./bin/zkServer.sh status
ZooKeeper JMX enabled by default
Using config: /conf/zoo.cfg
Mode: follower
```

1
2
3
4
5

## [#](https://funtl.com/zh/apache-dubbo-zookeeper/基于-Docker-安装-Zookeeper.html#伪集群模式)伪集群模式

### [#](https://funtl.com/zh/apache-dubbo-zookeeper/基于-Docker-安装-Zookeeper.html#docker-compose-yml-5)docker-compose.yml

```text
version: '3.1'
services:
    zoo1:
        image: zookeeper
        restart: always
        hostname: zoo1
        ports:
            - 2181:2181
        environment:
            ZOO_MY_ID: 1
            ZOO_SERVERS: server.1=zoo1:2888:3888 server.2=zoo2:2888:3888 server.3=zoo3:2888:3888

    zoo2:
        image: zookeeper
        restart: always
        hostname: zoo2
        ports:
            - 2182:2181
        environment:
            ZOO_MY_ID: 2
            ZOO_SERVERS: server.1=zoo1:2888:3888 server.2=zoo2:2888:3888 server.3=zoo3:2888:3888

    zoo3:
        image: zookeeper
        restart: always
        hostname: zoo3
        ports:
            - 2183:2181
        environment:
            ZOO_MY_ID: 3
            ZOO_SERVERS: server.1=zoo1:2888:3888 server.2=zoo2:2888:3888 server.3=zoo3:2888:3888
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

### [#](https://funtl.com/zh/apache-dubbo-zookeeper/基于-Docker-安装-Zookeeper.html#验证是否安装成功-2)验证是否安装成功

- 分别以交互方式进入容器查看

```text
docker exec -it zookeeper_zoo1_1 /bin/bash
```

1

```text
docker exec -it zookeeper_zoo2_1 /bin/bash
```

1

```text
docker exec -it zookeeper_zoo3_1 /bin/bash
```

1

- 使用服务端工具检查服务器状态

```text
root@UbuntuBase:/usr/local/docker/zookeeper# docker exec -it zookeeper_zoo1_1 /bin/bash
bash-4.3# ./bin/zkServer.sh status
ZooKeeper JMX enabled by default
Using config: /conf/zoo.cfg
Mode: follower
```

1
2
3
4
5

```text
root@UbuntuBase:/usr/local/docker/zookeeper# docker exec -it zookeeper_zoo2_1 /bin/bash
bash-4.3# ./bin/zkServer.sh status
ZooKeeper JMX enabled by default
Using config: /conf/zoo.cfg
Mode: follower
```

1
2
3
4
5

```text
root@UbuntuBase:/usr/local/docker/zookeeper# docker exec -it zookeeper_zoo3_1 /bin/bash
bash-4.3# ./bin/zkServer.sh status
ZooKeeper JMX enabled by default
Using config: /conf/zoo.cfg
Mode: leader
```

1
2
3
4
5

从上面的验证结果可以看出：zoo1 为跟随者，zoo2 为跟随者，zoo3 为领导者

上次更新: 2018-12-31 18:51:48

← [Zookeeper 如何实现分布式锁](https://funtl.com/zh/apache-dubbo-zookeeper/Zookeeper-如何实现分布式锁.html)[附：Linux 下手动安装 Zookeeper ](https://funtl.com/zh/apache-dubbo-zookeeper/Linux-下手动安装-Zookeeper.html)→