# [#](https://funtl.com/zh/docker-compose/Docker-Compose-使用.html#docker-compose-使用)Docker Compose 使用

## [#](https://funtl.com/zh/docker-compose/Docker-Compose-使用.html#本节视频)本节视频

- [【视频】基础设施即服务-Docker Compose-基本使用](https://www.bilibili.com/video/av27548248)

## [#](https://funtl.com/zh/docker-compose/Docker-Compose-使用.html#术语)术语

首先介绍几个术语。

- 服务 (`service`)：一个应用容器，实际上可以运行多个相同镜像的实例。
- 项目 (`project`)：由一组关联的应用容器组成的一个完整业务单元。

可见，一个项目可以由多个服务（容器）关联而成，`Compose` 面向项目进行管理。

## [#](https://funtl.com/zh/docker-compose/Docker-Compose-使用.html#场景)场景

最常见的项目是 web 网站，该项目应该包含 web 应用和缓存。

下面我们用 `Python` 来建立一个能够记录页面访问次数的 web 网站。

### [#](https://funtl.com/zh/docker-compose/Docker-Compose-使用.html#web-应用)web 应用

新建文件夹，在该目录中编写 `app.py` 文件

```python
from flask import Flask
from redis import Redis

app = Flask(__name__)
redis = Redis(host='redis', port=6379)

@app.route('/')
def hello():
    count = redis.incr('hits')
    return 'Hello World! 该页面已被访问 {} 次。\n'.format(count)

if __name__ == "__main__":
    app.run(host="0.0.0.0", debug=True)
```

### [#](https://funtl.com/zh/docker-compose/Docker-Compose-使用.html#dockerfile)Dockerfile

编写 `Dockerfile` 文件，内容为

```docker
FROM python:3.6-alpine
ADD . /code
WORKDIR /code
RUN pip install redis flask
CMD ["python", "app.py"]
```

### [#](https://funtl.com/zh/docker-compose/Docker-Compose-使用.html#docker-compose-yml)docker-compose.yml

编写 `docker-compose.yml` 文件，这个是 Compose 使用的主模板文件。

```yaml
version: '3'
services:

  web:
    build: .
    ports:
     - "5000:5000"
     
  redis:
    image: "redis:alpine"
```

### [#](https://funtl.com/zh/docker-compose/Docker-Compose-使用.html#运行-compose-项目)运行 compose 项目

```bash
$ docker-compose up
```

此时访问本地 `5000` 端口，每次刷新页面，计数就会加 1。

上次更新: 2018-12-30 15:32:03

← [Docker Compose 安装与卸载](https://funtl.com/zh/docker-compose/Docker-Compose-安装与卸载.html)[Docker Compose 命令说明 ](https://funtl.com/zh/docker-compose/Docker-Compose-命令说明.html)→