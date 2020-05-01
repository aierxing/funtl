# [#](https://funtl.com/zh/spring-boot-mybatis/测试-MyBatis-操作数据库.html#测试-mybatis-操作数据库)测试 MyBatis 操作数据库

## [#](https://funtl.com/zh/spring-boot-mybatis/测试-MyBatis-操作数据库.html#本节视频)本节视频

- [【视频】微服务框架-SpringBoot-MyBatis-测试](https://www.bilibili.com/video/av27784176)

## [#](https://funtl.com/zh/spring-boot-mybatis/测试-MyBatis-操作数据库.html#使用-tk-mybatis-操作数据库)使用 tk.mybatis 操作数据库

经过之前章节一系列的配置之后，我们已经满足了使用 MyBaits 操作数据库的必要条件，下面是使用 tk.mybatis 操作数据库的例子。

我们以测试操作用户表为例（tb_user）

## [#](https://funtl.com/zh/spring-boot-mybatis/测试-MyBatis-操作数据库.html#修改入口类)修改入口类

需要使用 `@MapperScan` 注解来指定 Mapper 接口的路径

**PS：** 注意这里的 `@MapperScan` 注解是 `tk.mybatis.spring.annotation.MapperScan;` 包下的

```text
package com.funtl.hello.spring.boot;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import tk.mybatis.spring.annotation.MapperScan;

@SpringBootApplication
@MapperScan(basePackages = "com.funtl.hello.spring.boot.mapper")
public class HelloSpringBootApplication {

    public static void main(String[] args) {
        SpringApplication.run(HelloSpringBootApplication.class, args);
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

## [#](https://funtl.com/zh/spring-boot-mybatis/测试-MyBatis-操作数据库.html#创建测试类)创建测试类

```text
package com.funtl.hello.spring.boot;

import com.funtl.hello.spring.boot.entity.TbUser;
import com.funtl.hello.spring.boot.mapper.TbUserMapper;
import com.github.pagehelper.PageHelper;
import com.github.pagehelper.PageInfo;
import org.junit.Test;
import org.junit.runner.RunWith;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.test.annotation.Rollback;
import org.springframework.test.context.junit4.SpringRunner;
import org.springframework.transaction.annotation.Transactional;
import tk.mybatis.mapper.entity.Example;

import java.util.Date;
import java.util.List;

@RunWith(SpringRunner.class)
@SpringBootTest(classes = HelloSpringBootApplication.class)
@Transactional
@Rollback
public class MyBatisTests {

    /**
     * 注入数据查询接口
     */
    @Autowired
    private TbUserMapper tbUserMapper;

    /**
     * 测试插入数据
     */
    @Test
    public void testInsert() {
        // 构造一条测试数据
        TbUser tbUser = new TbUser();
        tbUser.setUsername("Lusifer");
        tbUser.setPassword("123456");
        tbUser.setPhone("15888888888");
        tbUser.setEmail("topsale@vip.qq.com");
        tbUser.setCreated(new Date());
        tbUser.setUpdated(new Date());

        // 插入数据
        tbUserMapper.insert(tbUser);
    }

    /**
     * 测试删除数据
     */
    @Test
    public void testDelete() {
        // 构造条件，等同于 DELETE from tb_user WHERE username = 'Lusifer'
        Example example = new Example(TbUser.class);
        example.createCriteria().andEqualTo("username", "Lusifer");

        // 删除数据
        tbUserMapper.deleteByExample(example);
    }

    /**
     * 测试修改数据
     */
    @Test
    public void testUpdate() {
        // 构造条件
        Example example = new Example(TbUser.class);
        example.createCriteria().andEqualTo("username", "Lusifer");

        // 构造一条测试数据
        TbUser tbUser = new TbUser();
        tbUser.setUsername("LusiferNew");
        tbUser.setPassword("123456");
        tbUser.setPhone("15888888888");
        tbUser.setEmail("topsale@vip.qq.com");
        tbUser.setCreated(new Date());
        tbUser.setUpdated(new Date());

        // 修改数据
        tbUserMapper.updateByExample(tbUser, example);
    }

    /**
     * 测试查询集合
     */
    @Test
    public void testSelect() {
        List<TbUser> tbUsers = tbUserMapper.selectAll();
        for (TbUser tbUser : tbUsers) {
            System.out.println(tbUser.getUsername());
        }
    }

    /**
     * 测试分页查询
     */
    @Test
    public void testPage() {
        // PageHelper 使用非常简单，只需要设置页码和每页显示笔数即可
        PageHelper.startPage(0, 2);

        // 设置分页查询条件
        Example example = new Example(TbUser.class);
        PageInfo<TbUser> pageInfo = new PageInfo<>(tbUserMapper.selectByExample(example));

        // 获取查询结果
        List<TbUser> tbUsers = pageInfo.getList();
        for (TbUser tbUser : tbUsers) {
            System.out.println(tbUser.getUsername());
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
55
56
57
58
59
60
61
62
63
64
65
66
67
68
69
70
71
72
73
74
75
76
77
78
79
80
81
82
83
84
85
86
87
88
89
90
91
92
93
94
95
96
97
98
99
100
101
102
103
104
105
106
107
108
109
110
111
112
113

## [#](https://funtl.com/zh/spring-boot-mybatis/测试-MyBatis-操作数据库.html#附：完整的-pom)附：完整的 POM

```text
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>com.funtl</groupId>
    <artifactId>hello-spring-boot</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <packaging>jar</packaging>

    <name>hello-spring-boot</name>
    <description></description>

    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.0.2.RELEASE</version>
        <relativePath/> <!-- lookup parent from repository -->
    </parent>

    <properties>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
        <project.reporting.outputEncoding>UTF-8</project.reporting.outputEncoding>
        <java.version>1.8</java.version>
    </properties>

    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-tomcat</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-thymeleaf</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-actuator</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>

        <dependency>
            <groupId>com.alibaba</groupId>
            <artifactId>druid-spring-boot-starter</artifactId>
            <version>1.1.10</version>
        </dependency>
        <dependency>
            <groupId>tk.mybatis</groupId>
            <artifactId>mapper-spring-boot-starter</artifactId>
            <version>2.0.2</version>
        </dependency>
        <dependency>
            <groupId>com.github.pagehelper</groupId>
            <artifactId>pagehelper-spring-boot-starter</artifactId>
            <version>1.2.5</version>
        </dependency>

        <dependency>
            <groupId>net.sourceforge.nekohtml</groupId>
            <artifactId>nekohtml</artifactId>
            <version>1.9.22</version>
        </dependency>
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <scope>runtime</scope>
        </dependency>
    </dependencies>

    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
                <configuration>
                    <mainClass>com.funtl.hello.spring.boot.HelloSpringBootApplication</mainClass>
                </configuration>
            </plugin>

            <plugin>
                <groupId>org.mybatis.generator</groupId>
                <artifactId>mybatis-generator-maven-plugin</artifactId>
                <version>1.3.5</version>
                <configuration>
                    <configurationFile>${basedir}/src/main/resources/generator/generatorConfig.xml</configurationFile>
                    <overwrite>true</overwrite>
                    <verbose>true</verbose>
                </configuration>
                <dependencies>
                    <dependency>
                        <groupId>mysql</groupId>
                        <artifactId>mysql-connector-java</artifactId>
                        <version>${mysql.version}</version>
                    </dependency>
                    <dependency>
                        <groupId>tk.mybatis</groupId>
                        <artifactId>mapper</artifactId>
                        <version>3.4.4</version>
                    </dependency>
                </dependencies>
            </plugin>
        </plugins>
    </build>

</project>
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
55
56
57
58
59
60
61
62
63
64
65
66
67
68
69
70
71
72
73
74
75
76
77
78
79
80
81
82
83
84
85
86
87
88
89
90
91
92
93
94
95
96
97
98
99
100
101
102
103
104
105
106
107
108
109
110
111
112
113

上次更新: 2018-12-31 18:51:48

← [使用 MyBatis 的 Maven 插件生成代码](https://funtl.com/zh/spring-boot-mybatis/使用-MyBatis-的-Maven-插件生成代码.html)