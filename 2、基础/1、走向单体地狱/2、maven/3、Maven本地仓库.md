# Maven 本地仓库

## [#](https://funtl.com/zh/maven/Maven-本地仓库.html#本节视频)本节视频

- [【视频】使用 Maven 构建应用-Maven 本地仓库](https://www.bilibili.com/video/av24451974/)

## [#](https://funtl.com/zh/maven/Maven-本地仓库.html#概述)概述

Maven 的本地资源库是用来存储所有项目的依赖关系(插件 Jar 和其他文件，这些文件被 Maven 下载)到本地文件夹。很简单，当你建立一个 Maven 项目，所有相关文件将被存储在你的 Maven 本地仓库。

默认情况下，Maven 的本地资源库默认为 `.m2` 目录文件夹：

- Unix/Mac OS X：`~/.m2`
- Windows：`C:\Documents and Settings\{your-username}\.m2`

通常情况下，可改变默认的 `.m2` 目录下的默认本地存储库文件夹到其他更有意义的名称，例如， maven-repo 找到 `{M2_HOME}\conf\setting.xml`, 更新 `localRepository` 到其它名称。

![img](https://funtl.com/assets/Lusifer1511452605.png)

执行之后，新的 Maven 本地存储库现在改为 `D:/apache-maven-3.5.2/repo`

![img](https://funtl.com/assets/Lusifer1511452727.png)

上次更新: 2019-4-19 19:23:15