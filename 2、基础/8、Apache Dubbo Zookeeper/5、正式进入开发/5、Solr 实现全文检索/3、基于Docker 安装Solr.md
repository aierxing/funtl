# [#](https://funtl.com/zh/apache-dubbo-codeing/基于-Docker-安装-Solr.html#基于-docker-安装-solr)基于 Docker 安装 Solr

## [#](https://funtl.com/zh/apache-dubbo-codeing/基于-Docker-安装-Solr.html#本节视频)本节视频

- [【视频】Dubbo 实现微服务架构-使用 Solr 实现全文检索-基于 Docker 安装 Solr](https://www.bilibili.com/video/av35454085/)

## [#](https://funtl.com/zh/apache-dubbo-codeing/基于-Docker-安装-Solr.html#基本部署)基本部署

### [#](https://funtl.com/zh/apache-dubbo-codeing/基于-Docker-安装-Solr.html#docker-compose-yml)docker-compose.yml

```text
version: '3.1'
services:
  solr:
    image: solr
    restart: always
    container_name: solr
    ports:
      - 8983:8983
```

1
2
3
4
5
6
7
8

### [#](https://funtl.com/zh/apache-dubbo-codeing/基于-Docker-安装-Solr.html#部署成功效果图)部署成功效果图

访问地址：http://192.168.10.131:8983/

![img](https://funtl.com/assets/Lusifer1512700142.png)

## [#](https://funtl.com/zh/apache-dubbo-codeing/基于-Docker-安装-Solr.html#什么是分词技术？)什么是分词技术？

分词技术就是搜索引擎针对用户提交查询的关键词串进行的查询处理后根据用户的关键词串用各种匹配方法进行分词的一种技术。

### [#](https://funtl.com/zh/apache-dubbo-codeing/基于-Docker-安装-Solr.html#中文分词算法分类)中文分词算法分类

- 基于字符串匹配

基于字符串匹配，即扫描字符串，如果发现字符串的子串和词相同，就算匹配，这类分词通常会加入一些启发式规则，比如“正向/反向最大匹配”,“长词优先”等策略，这类算法优点是速度块，都是O(n)时间复杂度，实现简单，效果尚可。缺点，就是对歧义和未登录词处理不好

歧义的列子：歧义的例子很简单"长春市/长春/药店" "长春/市长/春药/店" 未登录：即词典中没有出现的词，当然也就处理不好

ikanalyzer, paoding 等就是基于字符串匹配的分词

- 基于统计以及机器学习的分词方式

这类分词基于人工标注的词性和统计特征，对中文进行建模，即根据观测到的数据（标注好的语料）对模型参数进行估计，即训练。在分词阶段再通过模型计算各种分词出现的概率，将概率最大的分词结果作为最终结果。常见的序列标注模型有 HMM 和 CRF。

这类分词算法能很好处理歧义和未登录词问题，效果比前一类效果好，但是需要大量的人工标注数据，以及较慢的分词速度。

### [#](https://funtl.com/zh/apache-dubbo-codeing/基于-Docker-安装-Solr.html#什么是-ikanalyzer)什么是 IKAnalyzer

IKAnalyzer 是一个开源的，基于 Java 语言开发的轻量级的中文分词工具包，基于文本匹配，不需要投入大量人力进行训练和标注可以自定词典，方便加入特定领域的词语，能分出多粒度的结果

## [#](https://funtl.com/zh/apache-dubbo-codeing/基于-Docker-安装-Solr.html#部署-solr-并安装-ikanalyzer)部署 Solr 并安装 IKAnalyzer

创建一个名为 `/usr/local/docker/solr/ikanalyzer` 目录

- `/usr/local/docker/solr`：用于存放 docker-compose.yml 配置文件
- `/usr/local/docker/solr/ikanalyzer`：用于存放 Dockerfile 镜像配置文件

### [#](https://funtl.com/zh/apache-dubbo-codeing/基于-Docker-安装-Solr.html#docker-compose-yml-2)docker-compose.yml

```text
version: '3.1'
services:
  solr:
    build: ikanalyzer
    restart: always
    container_name: solr
    ports:
      - 8983:8983
    volumes:
      - ./solrdata:/opt/solrdata
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

### [#](https://funtl.com/zh/apache-dubbo-codeing/基于-Docker-安装-Solr.html#dockerfile)Dockerfile

```text
FROM solr
MAINTAINER Lusifer <topsale@vip.qq.com>

# 创建 Core
WORKDIR /opt/solr/server/solr
RUN mkdir ik_core
WORKDIR /opt/solr/server/solr/ik_core
RUN echo 'name=ik_core' > core.properties
RUN mkdir data
RUN cp -r ../configsets/sample_techproducts_configs/conf/ .

# 安装中文分词
WORKDIR /opt/solr/server/solr-webapp/webapp/WEB-INF/lib
ADD ik-analyzer-solr5-5.x.jar .
ADD solr-analyzer-ik-5.1.0.jar .
WORKDIR /opt/solr/server/solr-webapp/webapp/WEB-INF
ADD ext.dic .
ADD stopword.dic .
ADD IKAnalyzer.cfg.xml .

# 增加分词配置
COPY managed-schema /opt/solr/server/solr/ik_core/conf

WORKDIR /opt/solr
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

### [#](https://funtl.com/zh/apache-dubbo-codeing/基于-Docker-安装-Solr.html#部署成功效果图-2)部署成功效果图

访问地址：http://192.168.10.131:8983/

![img](https://funtl.com/assets/Lusifer1520779234.png)

上次更新: 2019-4-19 19:23:15

← [什么是搜索引擎](https://funtl.com/zh/apache-dubbo-codeing/什么是搜索引擎.html)[Solr 的基本操作 ](https://funtl.com/zh/apache-dubbo-codeing/Solr-的基本操作.html)→