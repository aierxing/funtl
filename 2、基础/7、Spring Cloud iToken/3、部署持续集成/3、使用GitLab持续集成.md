# [#](https://funtl.com/zh/spring-cloud-itoken-ci/使用-GitLab-持续集成.html#使用-gitlab-持续集成)使用 GitLab 持续集成

## [#](https://funtl.com/zh/spring-cloud-itoken-ci/使用-GitLab-持续集成.html#本节视频)本节视频

- [【视频】项目实战-iToken-部署持续集成-使用 GitLab 持续集成](https://www.bilibili.com/video/av28384193)

## [#](https://funtl.com/zh/spring-cloud-itoken-ci/使用-GitLab-持续集成.html#简介)简介

从 GitLab 8.0 开始，GitLab CI 就已经集成在 GitLab 中，我们只要在项目中添加一个 `.gitlab-ci.yml` 文件，然后添加一个 Runner，即可进行持续集成。 而且随着 GitLab 的升级，GitLab CI 变得越来越强大。

## [#](https://funtl.com/zh/spring-cloud-itoken-ci/使用-GitLab-持续集成.html#概念)概念

### [#](https://funtl.com/zh/spring-cloud-itoken-ci/使用-GitLab-持续集成.html#pipeline)Pipeline

一次 Pipeline 其实相当于一次构建任务，里面可以包含多个流程，如安装依赖、运行测试、编译、部署测试服务器、部署生产服务器等流程。

任何提交或者 Merge Request 的合并都可以触发 Pipeline，如下图所示：

```text
+------------------+           +----------------+
|                  |  trigger  |                |
|   Commit / MR    +---------->+    Pipeline    |
|                  |           |                |
+------------------+           +----------------+
```

1
2
3
4
5

### [#](https://funtl.com/zh/spring-cloud-itoken-ci/使用-GitLab-持续集成.html#stages)Stages

Stages 表示构建阶段，说白了就是上面提到的流程。我们可以在一次 Pipeline 中定义多个 Stages，这些 Stages 会有以下特点：

- 所有 Stages 会按照顺序运行，即当一个 Stage 完成后，下一个 Stage 才会开始
- 只有当所有 Stages 完成后，该构建任务 (Pipeline) 才会成功
- 如果任何一个 Stage 失败，那么后面的 Stages 不会执行，该构建任务 (Pipeline) 失败

因此，Stages 和 Pipeline 的关系就是：

```text
+--------------------------------------------------------+
|                                                        |
|  Pipeline                                              |
|                                                        |
|  +-----------+     +------------+      +------------+  |
|  |  Stage 1  |---->|   Stage 2  |----->|   Stage 3  |  |
|  +-----------+     +------------+      +------------+  |
|                                                        |
+--------------------------------------------------------+
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

### [#](https://funtl.com/zh/spring-cloud-itoken-ci/使用-GitLab-持续集成.html#jobs)Jobs

Jobs 表示构建工作，表示某个 Stage 里面执行的工作。我们可以在 Stages 里面定义多个 Jobs，这些 Jobs 会有以下特点：

- 相同 Stage 中的 Jobs 会并行执行
- 相同 Stage 中的 Jobs 都执行成功时，该 Stage 才会成功
- 如果任何一个 Job 失败，那么该 Stage 失败，即该构建任务 (Pipeline) 失败

所以，Jobs 和 Stage 的关系图就是：

```text
+------------------------------------------+
|                                          |
|  Stage 1                                 |
|                                          |
|  +---------+  +---------+  +---------+   |
|  |  Job 1  |  |  Job 2  |  |  Job 3  |   |
|  +---------+  +---------+  +---------+   |
|                                          |
+------------------------------------------+
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

上次更新: 2018-12-31 18:51:48

← 