# 解决 Maven 无法自动下载依赖的问题

## [#](https://funtl.com/zh/supplement1/解决-Maven-无法自动下载依赖的问题.html#现象说明)现象说明

无法下载依赖的情况大致有两类：

- 无法通过 Maven 中央仓库提供的坐标下载（也许是网络环境导致）
- 类库没有上传至中央仓库只能通过官网或其它渠道下载

## [#](https://funtl.com/zh/supplement1/解决-Maven-无法自动下载依赖的问题.html#解决思路)解决思路

- 手动管理依赖，将依赖按传统的方式放入 `libs` 目录中。缺点是需要手动管理依赖版本。
- 将依赖安装到本地仓库中，按照 Maven 下载依赖的优先级，会优先查找本地仓库中的依赖。缺点是协同开发时，其他开发人员会因为本地缺少依赖导致项目启动报错。

## [#](https://funtl.com/zh/supplement1/解决-Maven-无法自动下载依赖的问题.html#解决方案)解决方案

以上解决思路虽然都可以解决缺少依赖的问题，但操作起来不太优雅，此时我们可以使用 Maven 提供的插件来解决这个问题

`pom.xml` 增加插件配置，关键配置如下：

```xml
<build>
    <plugins>
        <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-install-plugin</artifactId>
            <version>2.5.2</version>
            <executions>
                <execution>
                    <id>install-external-kaptcha</id>
                    <!-- 触发时机：执行 mvn clean 命令时自动触发插件 -->
                    <phase>clean</phase>
                    <configuration>
                        <!-- 存放依赖文件的位置 -->
                        <file>${project.basedir}/libs/kaptcha-2.3.jar</file>
                        <repositoryLayout>default</repositoryLayout>
                        <!-- 自定义 groupId -->
                        <groupId>com.google.code.kaptcha</groupId>
                        <!-- 自定义 artifactId -->
                        <artifactId>kaptcha</artifactId>
                        <!-- 自定义版本号 -->
                        <version>2.3</version>
                        <!-- 打包方式 -->
                        <packaging>jar</packaging>
                        <!-- 是否自动生成 POM -->
                        <generatePom>true</generatePom>
                    </configuration>
                    <goals>
                        <goal>install-file</goal>
                    </goals>
                </execution>
            </executions>
        </plugin>
    </plugins>
</build>
```

通过该插件，我们可以在执行 `mvn clean` 命令时将项目所需要的依赖自动安装进本地仓库

上次更新: 2018-12-29 5:44:48