# [#](https://funtl.com/zh/apache-http-client/使用-Apache-HttpClient-解决通信问题.html#apache-httpclient)Apache HttpClient

## [#](https://funtl.com/zh/apache-http-client/使用-Apache-HttpClient-解决通信问题.html#本节视频)本节视频

- [【视频】解决模块间的通信问题-Apache HttpClient 简介](https://www.bilibili.com/video/av26109195)
- [【视频】解决模块间的通信问题-Apache HttpClient 使用实例](https://www.bilibili.com/video/av26109207)

## [#](https://funtl.com/zh/apache-http-client/使用-Apache-HttpClient-解决通信问题.html#简易架构图)简易架构图

![img](https://funtl.com/assets/Lusifer201807030001.png)

## [#](https://funtl.com/zh/apache-http-client/使用-Apache-HttpClient-解决通信问题.html#apache-httpclient-简介)Apache HttpClient 简介

> HttpClient 是 Apache Jakarta Common 下的子项目，用来提供高效的、最新的、功能丰富的支持 HTTP 协议的客户端编程工具包，并且它支持 HTTP 协议最新的版本和建议。HttpClient 已经应用在很多的项目中，比如 Apache Jakarta 上很著名的另外两个开源项目 Cactus 和 **HTMLUnit** 都使用了 HttpClient。

HttpClient 相比传统 JDK 自带的 `URLConnection`，增加了易用性和灵活性，它不仅是客户端发送 HTTP 请求变得容易，而且也方便了开发人员测试接口（基于 HTTP 协议的），即提高了开发的效率，也方便提高代码的健壮性。因此熟练掌握 HttpClient 是很重要的必修内容，掌握 HttpClient 后，相信对于 HTTP 协议的了解会更加深入。

## [#](https://funtl.com/zh/apache-http-client/使用-Apache-HttpClient-解决通信问题.html#apache-httpclient-特性)Apache HttpClient 特性

- 基于标准、纯净的 Java 语言。实现了 HTTP 1.0 和 HTTP 1.1
- 以可扩展的面向对象的结构实现了 HTTP 全部的方法（GET, POST, PUT, DELETE, HEAD, OPTIONS, and TRACE）。
- 支持 HTTPS 协议。
- 通过 HTTP 代理建立透明的连接。
- 利用 CONNECT 方法通过 HTTP 代理建立隧道的 HTTPS 连接。
- Basic, Digest, NTLMv1, NTLMv2, NTLM2 Session, SNPNEGO/Kerberos 认证方案。
- 插件式的自定义认证方案。
- 便携可靠的套接字工厂使它更容易的使用第三方解决方案。
- 连接管理器支持多线程应用。支持设置最大连接数，同时支持设置每个主机的最大连接数，发现并关闭过期的连接。
- 自动处理 Set-Cookie 中的 Cookie。
- 插件式的自定义 Cookie 策略。
- Request 的输出流可以避免流中内容直接缓冲到 Socket 服务器。
- Response 的输入流可以有效的从 Socket 服务器直接读取相应内容。
- 在 HTTP 1.0 和 HTTP 1.1 中利用 KeepAlive 保持持久连接。
- 直接获取服务器发送的 response code 和 headers。
- 设置连接超时的能力。
- 实验性的支持 HTTP 1.1 response caching。
- 源代码基于 Apache License 可免费获取。

## [#](https://funtl.com/zh/apache-http-client/使用-Apache-HttpClient-解决通信问题.html#apache-httpclient-使用流程)Apache HttpClient 使用流程

使用 HttpClient 发送请求、接收响应很简单，一般需要如下几步即可。

- 创建 `HttpClient` 对象。
- 创建请求方法的实例，并指定请求 URL。如果需要发送 GET 请求，创建 `HttpGet` 对象；如果需要发送 POST 请求，创建 `HttpPost` 对象。
- 如果需要发送请求参数，可调用 `HttpGet`、`HttpPost` 共同的 `setParams(HttpParams params)` 方法来添加请求参数；对于 `HttpPost` 对象而言，也可调用 `setEntity(HttpEntity entity)` 方法来设置请求参数。
- 调用 `HttpClient` 对象的 `execute(HttpUriRequest request)` 发送请求，该方法返回一个 `HttpResponse`。
- 调用 `HttpResponse` 的 `getAllHeaders()`、`getHeaders(String name)` 等方法可获取服务器的响应头；调用 `HttpResponse` 的 `getEntity()` 方法可获取 `HttpEntity` 对象，该对象包装了服务器的响应内容。程序可通过该对象获取服务器的响应内容。
- 释放连接。无论执行方法是否成功，都必须释放连接

## [#](https://funtl.com/zh/apache-http-client/使用-Apache-HttpClient-解决通信问题.html#apache-httpclient-使用实例)Apache HttpClient 使用实例

### [#](https://funtl.com/zh/apache-http-client/使用-Apache-HttpClient-解决通信问题.html#pom)POM

`pom.xml` 配置如下：

```xml
<!-- Apache Http Begin -->
<dependency>
    <groupId>org.apache.httpcomponents</groupId>
    <artifactId>httpclient</artifactId>
    <version>4.5.5</version>
</dependency>
<dependency>
    <groupId>org.apache.httpcomponents</groupId>
    <artifactId>fluent-hc</artifactId>
    <version>4.5.5</version>
</dependency>
<dependency>
    <groupId>org.apache.httpcomponents</groupId>
    <artifactId>httpmime</artifactId>
    <version>4.5.5</version>
</dependency>
<!-- Apache Http End -->
```

主要增加了 `org.apache.httpcomponents:httpclient`、`org.apache.httpcomponents:fluent-hc`、`org.apache.httpcomponents:httpmime` 三个依赖

### [#](https://funtl.com/zh/apache-http-client/使用-Apache-HttpClient-解决通信问题.html#创建-httpget-请求)创建 HttpGet 请求

案例代码如下：

```java
package com.funtl.hello.httpclient;

import org.apache.http.HttpEntity;
import org.apache.http.client.methods.CloseableHttpResponse;
import org.apache.http.client.methods.HttpGet;
import org.apache.http.impl.client.CloseableHttpClient;
import org.apache.http.impl.client.HttpClients;
import org.apache.http.util.EntityUtils;

import java.io.IOException;

public class MyTest {
    public static void main(String[] args) {
        get();
    }

    private static void get() {
        // 创建 HttpClient 客户端
        CloseableHttpClient httpClient = HttpClients.createDefault();

        // 创建 HttpGet 请求
        HttpGet httpGet = new HttpGet("http://localhost:8080/content/page?draw=1&start=0&length=10");
        // 设置长连接
        httpGet.setHeader("Connection", "keep-alive");
        // 设置代理（模拟浏览器版本）
        httpGet.setHeader("User-Agent", "Mozilla/5.0 (Windows NT 10.0; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/63.0.3239.132 Safari/537.36");
        // 设置 Cookie
        httpGet.setHeader("Cookie", "UM_distinctid=16442706a09352-0376059833914f-3c604504-1fa400-16442706a0b345; CNZZDATA1262458286=1603637673-1530123020-%7C1530123020; JSESSIONID=805587506F1594AE02DC45845A7216A4");

        CloseableHttpResponse httpResponse = null;
        try {
            // 请求并获得响应结果
            httpResponse = httpClient.execute(httpGet);
            HttpEntity httpEntity = httpResponse.getEntity();
            // 输出请求结果
            System.out.println(EntityUtils.toString(httpEntity));
        } catch (IOException e) {
            e.printStackTrace();
        }

        // 无论如何必须关闭连接
        finally {
            if (httpResponse != null) {
                try {
                    httpResponse.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }

            if (httpClient != null) {
                try {
                    httpClient.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
        }
    }
}
```

控制台输出结果：

```text
{"draw":1,"recordsTotal":1,"recordsFiltered":1,"data":[{"id":33,"created":1530542074000,"updated":1530542074000,"title":"ad1","subTitle":"ad1","titleDesc":"ad1","url":"https://sale.jd.com/act/XkCzhoisOMSW.html","pic":"https://m.360buyimg.com/babel/jfs/t20164/187/1771326168/92964/b42fade7/5b359ab2N93be3a65.jpg","pic2":"","content":"<p><br></p>","tbContentCategory":{"id":89,"created":null,"updated":null,"parent":null,"isParent":null,"name":"幻灯片","status":null,"sortOrder":null}}],"error":null}
```

### [#](https://funtl.com/zh/apache-http-client/使用-Apache-HttpClient-解决通信问题.html#创建-httppost-请求)创建 HttpPost 请求

案例代码如下：

```java
package com.funtl.hello.httpclient;

import org.apache.http.HttpEntity;
import org.apache.http.client.ClientProtocolException;
import org.apache.http.client.entity.UrlEncodedFormEntity;
import org.apache.http.client.methods.CloseableHttpResponse;
import org.apache.http.client.methods.HttpPost;
import org.apache.http.impl.client.CloseableHttpClient;
import org.apache.http.impl.client.HttpClients;
import org.apache.http.message.BasicNameValuePair;
import org.apache.http.util.EntityUtils;

import java.io.IOException;
import java.io.UnsupportedEncodingException;
import java.util.ArrayList;
import java.util.List;

public class MyTest {
    public static void main(String[] args) {
        post();
    }

    private static void post() {
        // 创建 HttpClient 客户端
        CloseableHttpClient httpClient = HttpClients.createDefault();

        // 创建 HttpPost 请求
        HttpPost httpPost = new HttpPost("http://localhost:8080/content/page");
        // 设置长连接
        httpPost.setHeader("Connection", "keep-alive");
        // 设置代理（模拟浏览器版本）
        httpPost.setHeader("User-Agent", "Mozilla/5.0 (Windows NT 10.0; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/63.0.3239.132 Safari/537.36");
        // 设置 Cookie
        httpPost.setHeader("Cookie", "UM_distinctid=16442706a09352-0376059833914f-3c604504-1fa400-16442706a0b345; CNZZDATA1262458286=1603637673-1530123020-%7C1530123020; JSESSIONID=805587506F1594AE02DC45845A7216A4");

        // 创建 HttpPost 参数
        List<BasicNameValuePair> params = new ArrayList<BasicNameValuePair>();
        params.add(new BasicNameValuePair("draw", "1"));
        params.add(new BasicNameValuePair("start", "0"));
        params.add(new BasicNameValuePair("length", "10"));

        CloseableHttpResponse httpResponse = null;
        try {
            // 设置 HttpPost 参数
            httpPost.setEntity(new UrlEncodedFormEntity(params, "UTF-8"));
            httpResponse = httpClient.execute(httpPost);
            HttpEntity httpEntity = httpResponse.getEntity();
            // 输出请求结果
            System.out.println(EntityUtils.toString(httpEntity));
        } catch (UnsupportedEncodingException e) {
            e.printStackTrace();
        } catch (ClientProtocolException e) {
            e.printStackTrace();
        } catch (IOException e) {
            e.printStackTrace();
        }

        // 无论如何必须关闭连接
        finally {
            try {
                if (httpResponse != null) {
                    httpResponse.close();
                }
            } catch (IOException e) {
                e.printStackTrace();
            }

            try {
                if (httpClient != null) {
                    httpClient.close();
                }
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
    }
}
```

控制台输出结果：

```text
{"draw":1,"recordsTotal":1,"recordsFiltered":1,"data":[{"id":33,"created":1530542074000,"updated":1530542074000,"title":"ad1","subTitle":"ad1","titleDesc":"ad1","url":"https://sale.jd.com/act/XkCzhoisOMSW.html","pic":"https://m.360buyimg.com/babel/jfs/t20164/187/1771326168/92964/b42fade7/5b359ab2N93be3a65.jpg","pic2":"","content":"<p><br></p>","tbContentCategory":{"id":89,"created":null,"updated":null,"parent":null,"isParent":null,"name":"幻灯片","status":null,"sortOrder":null}}],"error":null}
```

上次更新: 2019-4-19 19:23:15