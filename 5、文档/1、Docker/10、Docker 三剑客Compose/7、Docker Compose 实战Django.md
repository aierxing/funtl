# [#](https://funtl.com/zh/docs-docker/Docker-Compose-实战-Django.html#docker-compose-实战-django)Docker Compose 实战 Django

本小节内容适合 `Python` 开发人员阅读。

我们现在将使用 `Docker Compose` 配置并运行一个 `Django/PostgreSQL` 应用。

在一切工作开始前，需要先编辑好三个必要的文件。

第一步，因为应用将要运行在一个满足所有环境依赖的 Docker 容器里面，那么我们可以通过编辑 `Dockerfile` 文件来指定 Docker 容器要安装内容。内容如下：

```docker
FROM python:3
ENV PYTHONUNBUFFERED 1
RUN mkdir /code
WORKDIR /code
ADD requirements.txt /code/
RUN pip install -r requirements.txt
ADD . /code/
```

1
2
3
4
5
6
7

以上内容指定应用将使用安装了 Python 以及必要依赖包的镜像。更多关于如何编写 `Dockerfile` 文件的信息可以查看 [镜像创建](../image/create.md#利用 Dockerfile 来创建镜像) 和 [Dockerfile 使用](https://funtl.com/zh/dockerfile/)。

第二步，在 `requirements.txt` 文件里面写明需要安装的具体依赖包名。

```bash
Django>=1.8,<2.0
psycopg2
```

1
2

第三步，`docker-compose.yml` 文件将把所有的东西关联起来。它描述了应用的构成（一个 web 服务和一个数据库）、使用的 Docker 镜像、镜像之间的连接、挂载到容器的卷，以及服务开放的端口。

```yaml
version: "3"
services:

  db:
    image: postgres

  web:
    build: .
    command: python3 manage.py runserver 0.0.0.0:8000
    volumes:
      - .:/code
    ports:
      - "8000:8000"
    links:
      - db
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

现在我们就可以使用 `docker-compose run` 命令启动一个 `Django` 应用了。

```bash
$ docker-compose run web django-admin.py startproject django_example .
```

1

Compose 会先使用 `Dockerfile` 为 web 服务创建一个镜像，接着使用这个镜像在容器里运行 `django-admin.py startproject composeexample` 指令。

这将在当前目录生成一个 `Django` 应用。

```bash
$ ls
Dockerfile       docker-compose.yml          django_example       manage.py       requirements.txt
```

1
2

如果你的系统是 Linux,记得更改文件权限。

```bash
sudo chown -R $USER:$USER .
```

1

首先，我们要为应用设置好数据库的连接信息。用以下内容替换 `django_example/settings.py` 文件中 `DATABASES = ...` 定义的节点内容。

```python
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.postgresql',
        'NAME': 'postgres',
        'USER': 'postgres',
        'HOST': 'db',
        'PORT': 5432,
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

这些信息是在 [postgres](https://store.docker.com/images/postgres/) 镜像固定设置好的。然后，运行 `docker-compose up` ：

```bash
$ docker-compose up

django_db_1 is up-to-date
Creating django_web_1 ...
Creating django_web_1 ... done
Attaching to django_db_1, django_web_1
db_1   | The files belonging to this database system will be owned by user "postgres".
db_1   | This user must also own the server process.
db_1   |
db_1   | The database cluster will be initialized with locale "en_US.utf8".
db_1   | The default database encoding has accordingly been set to "UTF8".
db_1   | The default text search configuration will be set to "english".

web_1  | Performing system checks...
web_1  |
web_1  | System check identified no issues (0 silenced).
web_1  |
web_1  | November 23, 2017 - 06:21:19
web_1  | Django version 1.11.7, using settings 'django_example.settings'
web_1  | Starting development server at http://0.0.0.0:8000/
web_1  | Quit the server with CONTROL-C.
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

这个 `Django` 应用已经开始在你的 Docker 守护进程里监听着 `8000` 端口了。打开 `127.0.0.1:8000` 即可看到 `Django` 欢迎页面。

你还可以在 Docker 上运行其它的管理命令，例如对于同步数据库结构这种事，在运行完 `docker-compose up` 后，在另外一个终端进入文件夹运行以下命令即可：

```bash
$ docker-compose run web python manage.py syncdb
```

1

上次更新: 2019-1-3 2:03:12

← [Docker Compose 模板文件](https://funtl.com/zh/docs-docker/Docker-Compose-模板文件.html)[Docker Compose 实战 Rails ](https://funtl.com/zh/docs-docker/Docker-Compose-实战-Rails.html)→