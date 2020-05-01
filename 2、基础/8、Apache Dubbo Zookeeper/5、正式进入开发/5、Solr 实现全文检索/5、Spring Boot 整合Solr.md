# [#](https://funtl.com/zh/apache-dubbo-codeing/Spring-Boot-整合-Solr.html#spring-boot-整合-solr)Spring Boot 整合 Solr

## [#](https://funtl.com/zh/apache-dubbo-codeing/Spring-Boot-整合-Solr.html#本节视频)本节视频

- [【视频】Dubbo 实现微服务架构-使用 Solr 实现全文检索-SpringBoot 整合 Solr](https://www.bilibili.com/video/av35516826/)

## [#](https://funtl.com/zh/apache-dubbo-codeing/Spring-Boot-整合-Solr.html#创建搜索服务接口)创建搜索服务接口

创建一个名为 `myshop-service-search-api` 项目，该项目只负责定义接口

### [#](https://funtl.com/zh/apache-dubbo-codeing/Spring-Boot-整合-Solr.html#pom)POM

```text
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <parent>
        <groupId>com.funtl</groupId>
        <artifactId>myshop-dependencies</artifactId>
        <version>1.0.0-SNAPSHOT</version>
        <relativePath>../myshop-dependencies/pom.xml</relativePath>
    </parent>

    <artifactId>myshop-service-search-api</artifactId>
    <packaging>jar</packaging>

    <url>http://www.funtl.com</url>
    <inceptionYear>2018-Now</inceptionYear>
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

### [#](https://funtl.com/zh/apache-dubbo-codeing/Spring-Boot-整合-Solr.html#tbitemresult)TbItemResult

为了实现全文检索的功能，我们需要将几张表的内容汇总到一个结果集中，此时需要使用多表联查将结果集汇总，SQL 语句如下：

```text
SELECT
  a.id,
  a.cid AS tb_item_cid,
  b.name AS tb_item_cname,
  a.title AS tb_item_title,
  a.sell_point AS tb_item_sell_point,
  c.item_desc AS tb_item_desc
FROM
  tb_item AS a
  LEFT JOIN tb_item_cat AS b
    ON a.cid = b.id
  LEFT JOIN tb_item_desc AS c
    ON c.item_id = a.id
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

实体类如下：

```text
package com.funtl.myshop.service.search.domain;

import java.io.Serializable;

public class TbItemResult implements Serializable {
    private Long id;
    private Long tbItemCid;
    private String tbItemCname;
    private String tbItemTitle;
    private String tbItemSellPoint;
    private String tbItemDesc;

    public Long getId() {
        return id;
    }

    public void setId(Long id) {
        this.id = id;
    }

    public Long getTbItemCid() {
        return tbItemCid;
    }

    public void setTbItemCid(Long tbItemCid) {
        this.tbItemCid = tbItemCid;
    }

    public String getTbItemCname() {
        return tbItemCname;
    }

    public void setTbItemCname(String tbItemCname) {
        this.tbItemCname = tbItemCname;
    }

    public String getTbItemTitle() {
        return tbItemTitle;
    }

    public void setTbItemTitle(String tbItemTitle) {
        this.tbItemTitle = tbItemTitle;
    }

    public String getTbItemSellPoint() {
        return tbItemSellPoint;
    }

    public void setTbItemSellPoint(String tbItemSellPoint) {
        this.tbItemSellPoint = tbItemSellPoint;
    }

    public String getTbItemDesc() {
        return tbItemDesc;
    }

    public void setTbItemDesc(String tbItemDesc) {
        this.tbItemDesc = tbItemDesc;
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

### [#](https://funtl.com/zh/apache-dubbo-codeing/Spring-Boot-整合-Solr.html#searchservice)SearchService

```text
package com.funtl.myshop.service.search.api;

import com.funtl.myshop.service.search.domain.TbItemResult;

import java.util.List;

public interface SearchService {
    /**
     * 搜索商品
     * @param query 查询关键字
     * @param page 页码
     * @param rows 笔数
     * @return
     */
    List<TbItemResult> search(String query, int page, int rows);
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

## [#](https://funtl.com/zh/apache-dubbo-codeing/Spring-Boot-整合-Solr.html#创建搜索服务提供者)创建搜索服务提供者

创建一个名为 `myshop-service-search-provider` 项目，该项目负责实现查询接口并初始化 Solr

### [#](https://funtl.com/zh/apache-dubbo-codeing/Spring-Boot-整合-Solr.html#pom-2)POM

```text
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <parent>
        <groupId>com.funtl</groupId>
        <artifactId>myshop-dependencies</artifactId>
        <version>1.0.0-SNAPSHOT</version>
        <relativePath>../myshop-dependencies/pom.xml</relativePath>
    </parent>

    <artifactId>myshop-service-search-provider</artifactId>
    <packaging>jar</packaging>

    <url>http://www.funtl.com</url>
    <inceptionYear>2018-Now</inceptionYear>

    <dependencies>
        <!-- Spring Boot Starter Settings -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-data-solr</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>

        <!-- Commons Settings -->
        <dependency>
            <groupId>de.javakaffee</groupId>
            <artifactId>kryo-serializers</artifactId>
        </dependency>

        <!-- Projects Settings -->
        <dependency>
            <groupId>com.funtl</groupId>
            <artifactId>myshop-commons-dubbo</artifactId>
            <version>${project.parent.version}</version>
        </dependency>
        <dependency>
            <groupId>com.funtl</groupId>
            <artifactId>myshop-commons-mapper</artifactId>
            <version>${project.parent.version}</version>
        </dependency>
        <dependency>
            <groupId>com.funtl</groupId>
            <artifactId>myshop-service-search-api</artifactId>
            <version>${project.parent.version}</version>
        </dependency>
    </dependencies>

    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
                <configuration>
                    <mainClass>com.funtl.myshop.service.search.provider.MyShopServiceSearchProviderApplication</mainClass>
                </configuration>
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

主要增加了 `org.springframework.boot:spring-boot-starter-data-solr` 依赖

### [#](https://funtl.com/zh/apache-dubbo-codeing/Spring-Boot-整合-Solr.html#tbitemresultmapper)TbItemResultMapper

创建原生 Mapper ，主要作用是通过多表联查将数据汇总

```text
package com.funtl.myshop.service.search.provider.mapper;

import com.funtl.myshop.service.search.domain.TbItemResult;
import org.springframework.stereotype.Repository;

import java.util.List;

@Repository
public interface TbItemResultMapper {
    List<TbItemResult> selectAll();
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

- `TbItemResultMapper.xml`

```text
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.funtl.myshop.service.search.provider.mapper.TbItemResultMapper">
    <resultMap id="BaseResultMap" type="com.funtl.myshop.service.search.domain.TbItemResult">
        <id column="id" jdbcType="BIGINT" property="id" />
        <result column="tb_item_cid" jdbcType="BIGINT" property="tbItemCid" />
        <result column="tb_item_cname" jdbcType="VARCHAR" property="tbItemCname" />
        <result column="tb_item_title" jdbcType="VARCHAR" property="tbItemTitle" />
        <result column="tb_item_sell_point" jdbcType="VARCHAR" property="tbItemSellPoint" />
        <result column="tb_item_desc" jdbcType="VARCHAR" property="tbItemDesc" />
    </resultMap>

    <select id="selectAll" resultMap="BaseResultMap">
        SELECT
          a.id,
          a.cid AS tb_item_cid,
          b.name AS tb_item_cname,
          a.title AS tb_item_title,
          a.sell_point AS tb_item_sell_point,
          c.item_desc AS tb_item_desc
        FROM
          tb_item AS a
          LEFT JOIN tb_item_cat AS b
            ON a.cid = b.id
          LEFT JOIN tb_item_desc AS c
            ON c.item_id = a.id
    </select>
</mapper>
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

### [#](https://funtl.com/zh/apache-dubbo-codeing/Spring-Boot-整合-Solr.html#application)Application

这里使用 Spring 提供的 MyBatis 包扫面注解

```text
package com.funtl.myshop.service.search.provider;

import com.alibaba.dubbo.container.Main;
import org.mybatis.spring.annotation.MapperScan;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.netflix.hystrix.EnableHystrix;
import org.springframework.cloud.netflix.hystrix.dashboard.EnableHystrixDashboard;
import org.springframework.transaction.annotation.EnableTransactionManagement;

@EnableHystrix
@EnableHystrixDashboard
@SpringBootApplication(scanBasePackages = "com.funtl.myshop")
@EnableTransactionManagement
@MapperScan(basePackages = "com.funtl.myshop.service.search.provider.mapper")
public class MyShopServiceSearchProviderApplication {
    public static void main(String[] args) {
        SpringApplication.run(MyShopServiceSearchProviderApplication.class, args);
        Main.main(args);
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

### [#](https://funtl.com/zh/apache-dubbo-codeing/Spring-Boot-整合-Solr.html#测试并初始化-solr)测试并初始化 Solr

```text
package com.funtl.myshop.service.search.provider.test;

import com.funtl.myshop.service.search.domain.TbItemResult;
import com.funtl.myshop.service.search.provider.MyShopServiceSearchProviderApplication;
import com.funtl.myshop.service.search.provider.mapper.TbItemResultMapper;
import org.apache.solr.client.solrj.SolrClient;
import org.apache.solr.client.solrj.SolrQuery;
import org.apache.solr.client.solrj.SolrServerException;
import org.apache.solr.client.solrj.response.QueryResponse;
import org.apache.solr.common.SolrDocument;
import org.apache.solr.common.SolrDocumentList;
import org.apache.solr.common.SolrInputDocument;
import org.junit.Test;
import org.junit.runner.RunWith;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.test.context.junit4.SpringRunner;

import java.io.IOException;
import java.util.List;
import java.util.Map;

@RunWith(SpringRunner.class)
@SpringBootTest(classes = MyShopServiceSearchProviderApplication.class)
public class SearchServiceTest {

    @Autowired
    private SolrClient solrClient;

    @Autowired
    private TbItemResultMapper tbItemResultMapper;

    /**
     * 初始化 Solr
     */
    @Test
    public void testInitSolr() {
        List<TbItemResult> tbItemResults = tbItemResultMapper.selectAll();

        SolrInputDocument document = null;
        for (TbItemResult tbItemResult : tbItemResults) {
            document = new SolrInputDocument();
            document.addField("id", tbItemResult.getId());
            document.addField("tb_item_cid", tbItemResult.getTbItemCid());
            document.addField("tb_item_cname", tbItemResult.getTbItemCname());
            document.addField("tb_item_title", tbItemResult.getTbItemTitle());
            document.addField("tb_item_sell_point", tbItemResult.getTbItemSellPoint());
            document.addField("tb_item_desc", tbItemResult.getTbItemDesc());

            try {
                solrClient.add(document);
                solrClient.commit();
            } catch (SolrServerException e) {
                e.printStackTrace();
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
    }

    /**
     * 添加索引库
     */
    @Test
    public void testAddDocument() {
        // 创建文档对象
        SolrInputDocument document = new SolrInputDocument();

        // 向文档中添加域
        document.addField("id", 536563);
        document.addField("tb_item_title", "new2 - 阿尔卡特 (OT-927) 炭黑 联通3G手机 双卡双待");

        // 将文档添加到索引库
        try {
            solrClient.add(document);
            solrClient.commit();
        } catch (SolrServerException e) {
            e.printStackTrace();
        } catch (IOException e) {
            e.printStackTrace();
        }
    }

    /**
     * 删除索引库
     */
    @Test
    public void testDeleteDocument() {
        try {
            solrClient.deleteByQuery("*:*");
            solrClient.commit();
        } catch (SolrServerException e) {
            e.printStackTrace();
        } catch (IOException e) {
            e.printStackTrace();
        }
    }

    /**
     * 查询索引库
     */
    @Test
    public void testQueryDocument() {
        // 创建查询对象
        SolrQuery query = new SolrQuery();

        // 设置查询条件
        query.setQuery("手机");

        // 设置分页
        query.setStart(0);
        query.setRows(10);

        // 设置默认搜索域
        query.set("df", "tb_item_keywords");

        // 设置高亮显示
        query.setHighlight(true);
        query.addHighlightField("tb_item_title");
        query.setHighlightSimplePre("<span style='color:red;'>");
        query.setHighlightSimplePost("</span>");

        try {
            // 执行查询操作
            QueryResponse response = solrClient.query(query);

            // 获取查询结果集
            SolrDocumentList results = response.getResults();

            // 获取高亮显示
            Map<String, Map<String, List<String>>> highlighting = response.getHighlighting();

            // 遍历结果集
            for (SolrDocument result : results) {
                String tbItemTitle = "";
                List<String> strings = highlighting.get(result.get("id")).get("tb_item_title");
                if (strings != null && strings.size() > 0) {
                    tbItemTitle = strings.get(0);
                } else {
                    tbItemTitle = (String) result.get("tb_item_title");
                }

                System.out.println(tbItemTitle);
            }
        } catch (SolrServerException e) {
            e.printStackTrace();
        } catch (IOException e) {
            e.printStackTrace();
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
114
115
116
117
118
119
120
121
122
123
124
125
126
127
128
129
130
131
132
133
134
135
136
137
138
139
140
141
142
143
144
145
146
147
148
149
150
151

### [#](https://funtl.com/zh/apache-dubbo-codeing/Spring-Boot-整合-Solr.html#searchserviceimpl)SearchServiceImpl

```text
package com.funtl.myshop.service.search.provider.api.impl;

import com.alibaba.dubbo.config.annotation.Service;
import com.funtl.myshop.service.search.api.SearchService;
import com.funtl.myshop.service.search.domain.TbItemResult;
import com.google.common.collect.Lists;
import org.apache.solr.client.solrj.SolrClient;
import org.apache.solr.client.solrj.SolrQuery;
import org.apache.solr.client.solrj.SolrServerException;
import org.apache.solr.client.solrj.response.QueryResponse;
import org.apache.solr.common.SolrDocument;
import org.apache.solr.common.SolrDocumentList;
import org.springframework.beans.factory.annotation.Autowired;

import java.io.IOException;
import java.util.List;
import java.util.Map;

@Service(version = "${services.versions.search.v1}")
public class SearchServiceImpl implements SearchService {

    @Autowired
    private SolrClient solrClient;

    @Override
    public List<TbItemResult> search(String query, int page, int rows) {
        List<TbItemResult> searchResults = Lists.newArrayList();

        // 创建查询对象
        SolrQuery solrQuery = new SolrQuery();

        // 设置查询条件
        solrQuery.setQuery(query);

        // 设置分页条件
        solrQuery.setStart((page - 1) * rows);
        solrQuery.setRows(rows);

        // 设置默认搜索域
        solrQuery.set("df", "tb_item_keywords");

        // 设置高亮显示
        solrQuery.setHighlight(true);
        solrQuery.addHighlightField("tb_item_title");
        solrQuery.setHighlightSimplePre("<span style='color:red'>");
        solrQuery.setHighlightSimplePost("</span>");

        try {
            // 执行查询操作
            QueryResponse queryResponse = solrClient.query(solrQuery);
            SolrDocumentList solrDocuments = queryResponse.getResults();
            Map<String, Map<String, List<String>>> highlighting = queryResponse.getHighlighting();
            for (SolrDocument solrDocument : solrDocuments) {
                TbItemResult result = new TbItemResult();

                result.setId(Long.parseLong(String.valueOf(solrDocument.get("id"))));
                result.setTbItemCid(Long.parseLong(String.valueOf(solrDocument.get("tb_item_cid"))));
                result.setTbItemCname((String) solrDocument.get("tb_item_cname"));
                result.setTbItemTitle((String) solrDocument.get("tb_item_title"));
                result.setTbItemSellPoint((String) solrDocument.get("tb_item_sell_point"));
                result.setTbItemDesc((String) solrDocument.get("tb_item_desc"));

                String tbItemTitle = "";
                List<String> list = highlighting.get(solrDocument.get("id")).get("tb_item_title");
                if (list != null && list.size() > 0) {
                    tbItemTitle = list.get(0);
                } else {
                    tbItemTitle = (String) solrDocument.get("tb_item_title");
                }
                result.setTbItemTitle(tbItemTitle);

                searchResults.add(result);
            }
        } catch (SolrServerException e) {
            e.printStackTrace();
        } catch (IOException e) {
            e.printStackTrace();
        }

        return searchResults;
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

### [#](https://funtl.com/zh/apache-dubbo-codeing/Spring-Boot-整合-Solr.html#application-yml)application.yml

主要增加了 `spring.data.solr.host` 配置：

```text
spring:
  data:
    solr:
      host: http://192.168.10.131:8983/solr/ik_core
```

1
2
3
4

完整配置如下：

```text
# Spring boot application
spring:
  application:
    name: myshop-service-search-provider
  datasource:
    druid:
      url: jdbc:mysql://192.168.10.131:3306/myshop?useUnicode=true&characterEncoding=utf-8&useSSL=false
      username: root
      password: 123456
      initial-size: 1
      min-idle: 1
      max-active: 20
      test-on-borrow: true
      driver-class-name: com.mysql.cj.jdbc.Driver
  data:
    solr:
      host: http://192.168.10.131:8983/solr/ik_core
server:
  port: 8504

# MyBatis Config properties
mybatis:
  type-aliases-package: com.funtl.myshop.commons.domain
  mapper-locations: classpath:mapper/*.xml

# Services Versions
services:
  versions:
    search:
      v1: 1.0.0

# Dubbo Config properties
dubbo:
  ## Base packages to scan Dubbo Component：@com.alibaba.dubbo.config.annotation.Service
  scan:
    basePackages: com.funtl.myshop.service.search.provider.api.impl
  ## ApplicationConfig Bean
  application:
    id: myshop-service-search-provider
    name: myshop-service-search-provider
    qos-port: 22225
    qos-enable: true
  ## ProtocolConfig Bean
  protocol:
    id: dubbo
    name: dubbo
    port: 20884
    status: server
    serialization: kryo
  ## RegistryConfig Bean
  registry:
    id: zookeeper
    address: zookeeper://192.168.10.131:2181?backup=192.168.10.131:2182,192.168.10.131:2183

# Enables Dubbo All Endpoints
management:
  endpoint:
    dubbo:
      enabled: true
    dubbo-shutdown:
      enabled: true
    dubbo-configs:
      enabled: true
    dubbo-services:
      enabled: true
    dubbo-references:
      enabled: true
    dubbo-properties:
      enabled: true
  # Dubbo Health
  health:
    dubbo:
      status:
        ## StatusChecker Name defaults (default : "memory", "load" )
        defaults: memory
        ## StatusChecker Name extras (default : empty )
        extras: load,threadpool


logging:
  level.com.funtl.myshop.commons.mapper: DEBUG
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

## [#](https://funtl.com/zh/apache-dubbo-codeing/Spring-Boot-整合-Solr.html#创建搜索服务消费者)创建搜索服务消费者

创建一个名为 `myshop-service-search-cosumer` 项目

### [#](https://funtl.com/zh/apache-dubbo-codeing/Spring-Boot-整合-Solr.html#pom-3)POM

```text
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <parent>
        <groupId>com.funtl</groupId>
        <artifactId>myshop-dependencies</artifactId>
        <version>1.0.0-SNAPSHOT</version>
        <relativePath>../myshop-dependencies/pom.xml</relativePath>
    </parent>

    <artifactId>myshop-service-search-consumer</artifactId>
    <packaging>jar</packaging>

    <url>http://www.funtl.com</url>
    <inceptionYear>2018-Now</inceptionYear>

    <dependencies>
        <!-- Spring Boot Starter Settings -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>

        <!-- Commons Settings -->
        <dependency>
            <groupId>de.javakaffee</groupId>
            <artifactId>kryo-serializers</artifactId>
        </dependency>

        <!-- Projects Settings -->
        <dependency>
            <groupId>com.funtl</groupId>
            <artifactId>myshop-commons-dubbo</artifactId>
            <version>${project.parent.version}</version>
        </dependency>
        <dependency>
            <groupId>com.funtl</groupId>
            <artifactId>myshop-static-backend</artifactId>
            <version>${project.parent.version}</version>
        </dependency>
        <dependency>
            <groupId>com.funtl</groupId>
            <artifactId>myshop-service-search-api</artifactId>
            <version>${project.parent.version}</version>
        </dependency>
    </dependencies>

    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
                <configuration>
                    <mainClass>com.funtl.myshop.service.search.consumer.MyShopServiceSearchConsumerApplication</mainClass>
                </configuration>
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

### [#](https://funtl.com/zh/apache-dubbo-codeing/Spring-Boot-整合-Solr.html#application-2)Application

```text
package com.funtl.myshop.service.search.consumer;

import com.alibaba.dubbo.container.Main;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.boot.autoconfigure.jdbc.DataSourceAutoConfiguration;
import org.springframework.cloud.netflix.hystrix.EnableHystrix;
import org.springframework.cloud.netflix.hystrix.dashboard.EnableHystrixDashboard;

@EnableHystrix
@EnableHystrixDashboard
@SpringBootApplication(scanBasePackages = "com.funtl.myshop", exclude = DataSourceAutoConfiguration.class)
public class MyShopServiceSearchConsumerApplication {
    public static void main(String[] args) {
        SpringApplication.run(MyShopServiceSearchConsumerApplication.class, args);
        Main.main(args);
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

### [#](https://funtl.com/zh/apache-dubbo-codeing/Spring-Boot-整合-Solr.html#application-yml-2)application.yml

```text
# Spring boot application
spring:
  application:
    name: myshop-service-search-consumer
  thymeleaf:
    cache: false # 开发时关闭缓存,不然没法看到实时页面
    mode: LEGACYHTML5 # 用非严格的 HTML
    encoding: UTF-8
    servlet:
      content-type: text/html
server:
  port: 8603

# Services Versions
services:
  versions:
    search:
      v1: 1.0.0

# Dubbo Config properties
dubbo:
  scan:
    basePackages: com.funtl.myshop.service.search.consumer
  ## ApplicationConfig Bean
  application:
    id: myshop-service-search-consumer
    name: myshop-service-search-consumer
  ## ProtocolConfig Bean
  protocol:
    id: dubbo
    name: dubbo
    port: 30883
    status: server
    serialization: kryo
  ## RegistryConfig Bean
  registry:
    id: zookeeper
    address: zookeeper://192.168.10.131:2181?backup=192.168.10.131:2182,192.168.10.131:2183

# Dubbo Endpoint (default status is disable)
endpoints:
  dubbo:
    enabled: true

management:
  server:
    port: 8703
  # Dubbo Health
  health:
    dubbo:
      status:
        ## StatusChecker Name defaults (default : "memory", "load" )
        defaults: memory
  # Enables Dubbo All Endpoints
  endpoint:
    dubbo:
      enabled: true
    dubbo-shutdown:
      enabled: true
    dubbo-configs:
      enabled: true
    dubbo-services:
      enabled: true
    dubbo-references:
      enabled: true
    dubbo-properties:
      enabled: true
  endpoints:
    web:
      exposure:
        include: "*"
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

### [#](https://funtl.com/zh/apache-dubbo-codeing/Spring-Boot-整合-Solr.html#controller)Controller

```text
package com.funtl.myshop.service.search.consumer.controller;

import com.alibaba.dubbo.config.annotation.Reference;
import com.funtl.myshop.service.search.api.SearchService;
import com.funtl.myshop.service.search.domain.TbItemResult;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestMethod;
import org.springframework.web.bind.annotation.RestController;

import java.util.List;

@RestController
public class SearchController {

    @Reference(version = "${services.versions.search.v1}")
    private SearchService searchService;

    @RequestMapping(value = "search/{query}/{page}/{size}", method = RequestMethod.GET)
    public List<TbItemResult> search(
            @PathVariable(required = true) String query,
            @PathVariable(required = true) int page,
            @PathVariable(required = true) int size) {
        List<TbItemResult> tbItemResults = searchService.search(query, page, size);
        return tbItemResults;
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

### [#](https://funtl.com/zh/apache-dubbo-codeing/Spring-Boot-整合-Solr.html#测试效果图)测试效果图

在浏览器访问：http://localhost:8603/search/手机/1/10

![img](https://funtl.com/assets/Lusifer_20181107031627.png)

上次更新: 2019-4-19 19:23:15

← [Solr 的基本操作](https://funtl.com/zh/apache-dubbo-codeing/Solr-的基本操作.html)[Apollo 简介 ](https://funtl.com/zh/apache-dubbo-codeing/Apollo-简介.html)→