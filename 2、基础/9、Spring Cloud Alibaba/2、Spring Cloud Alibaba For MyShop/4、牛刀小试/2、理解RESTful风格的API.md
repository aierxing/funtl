# [#](https://funtl.com/zh/spring-cloud-alibaba-myshop/理解-RESTful-风格的-API.html#理解-restful-风格的-api)理解 RESTful 风格的 API

## [#](https://funtl.com/zh/spring-cloud-alibaba-myshop/理解-RESTful-风格的-API.html#本节视频)本节视频

- [【视频】解决模块间的通信问题-RESTful 风格的 API 设计](https://www.bilibili.com/video/av26109248/)
- [【视频】解决模块间的通信问题-使用 SpringMVC 实现 RESTful 风格的 API](https://www.bilibili.com/video/av26109258/)

## [#](https://funtl.com/zh/spring-cloud-alibaba-myshop/理解-RESTful-风格的-API.html#概述)概述

2000 年，Roy Thomas Fielding 博士在他那篇著名的博士论文《Architectural Styles and the Design of Network-based Software Architectures》中提出了几种软件应用的架构风格，REST 作为其中的一种架构风格在这篇论文的第5章中进行了概括性的介绍。

REST 是“REpresentational State Transfer”的缩写，可以翻译成“表现状态转换”，但是在绝大多数场合中我们只说 REST 或者 RESTful。Fielding 在论文中将 REST 定位为“分布式超媒体应用（Distributed Hypermedia System）”的架构风格，它在文中提到一个名为“HATEOAS（Hypermedia as the engine of application state）”的概念。

我们利用一个面向最终用户的 Web 应用来对这个概念进行简单阐述：这里所谓的应用状态（Application State）表示 Web 应用的客户端的状态，简单起见可以理解为会话状态。资源在浏览器中以超媒体的形式呈现，通过点击超媒体中的链接可以获取其它相关的资源或者对当前资源进行相应的处理，获取的资源或者针对资源处理的响应同样以超媒体的形式再次呈现在浏览器上。由此可见，超媒体成为了驱动客户端会话状态的转换的引擎。

借助于超媒体这种特殊的资源呈现方式，应用状态的转换体现为浏览器中呈现资源的转换。如果将超媒体进一步抽象成一般意义上的资源呈现（Representation ）方式，那么应用状态变成了可被呈现的状态（REpresentational State）。应用状态之间的转换就成了可被呈现的状态装换（REpresentational State Transfer），这就是 REST。

REST 是一种很笼统的概念，它代表一种架构风格。

## [#](https://funtl.com/zh/spring-cloud-alibaba-myshop/理解-RESTful-风格的-API.html#怎么理解-restful)怎么理解 RESTful

### [#](https://funtl.com/zh/spring-cloud-alibaba-myshop/理解-RESTful-风格的-API.html#版本号)版本号

在 RESTful API 中，API 接口应该尽量兼容之前的版本。但是，在实际业务开发场景中，可能随着业务需求的不断迭代，现有的 API 接口无法支持旧版本的适配，此时如果强制升级服务端的 API 接口将导致客户端旧有功能出现故障。实际上，Web 端是部署在服务器，因此它可以很容易为了适配服务端的新的 API 接口进行版本升级，然而像 Android 端、IOS 端、PC 端等其他客户端是运行在用户的机器上，因此当前产品很难做到适配新的服务端的 API 接口，从而出现功能故障，这种情况下，用户必须升级产品到最新的版本才能正常使用。

为了解决这个版本不兼容问题，在设计 RESTful API 的一种实用的做法是使用版本号。一般情况下，我们会在 url 中保留版本号，并同时兼容多个版本。

```text
【GET】  /v1/users/{user_id}  // 版本 v1 的查询用户列表的 API 接口
【GET】  /v2/users/{user_id}  // 版本 v2 的查询用户列表的 API 接口
```

1
2

现在，我们可以不改变版本 v1 的查询用户列表的 API 接口的情况下，新增版本 v2 的查询用户列表的 API 接口以满足新的业务需求，此时，客户端的产品的新功能将请求新的服务端的 API 接口地址。虽然服务端会同时兼容多个版本，但是同时维护太多版本对于服务端而言是个不小的负担，因为服务端要维护多套代码。这种情况下，常见的做法不是维护所有的兼容版本，而是只维护最新的几个兼容版本，例如维护最新的三个兼容版本。在一段时间后，当绝大多数用户升级到较新的版本后，废弃一些使用量较少的服务端的老版本API 接口版本，并要求使用产品的非常旧的版本的用户强制升级。

注意的是，“不改变版本 v1 的查询用户列表的 API 接口”主要指的是对于客户端的调用者而言它看起来是没有改变。而实际上，如果业务变化太大，服务端的开发人员需要对旧版本的 API 接口使用适配器模式将请求适配到新的API 接口上。

### [#](https://funtl.com/zh/spring-cloud-alibaba-myshop/理解-RESTful-风格的-API.html#资源路径)资源路径

RESTful API 的设计以资源为核心，每一个 URI 代表一种资源。因此，URI 不能包含动词，只能是名词。注意的是，形容词也是可以使用的，但是尽量少用。一般来说，不论资源是单个还是多个，API 的名词要以复数进行命名。此外，命名名词的时候，要使用小写、数字及下划线来区分多个单词。这样的设计是为了与 json 对象及属性的命名方案保持一致。例如，一个查询系统标签的接口可以进行如下设计。

```text
【GET】  /v1/tags/{tag_id} 
```

1

同时，资源的路径应该从根到子依次如下

```text
/{resources}/{resource_id}/{sub_resources}/{sub_resource_id}/{sub_resource_property}
```

1

我们来看一个“添加用户的角色”的设计，其中“用户”是主资源，“角色”是子资源。

```text
【POST】  /v1/users/{user_id}/roles/{role_id} // 添加用户的角色
```

1

有的时候，当一个资源变化难以使用标准的 RESTful API 来命名，可以考虑使用一些特殊的 actions 命名。

```text
/{resources}/{resource_id}/actions/{action}
```

1

举个例子，“密码修改”这个接口的命名很难完全使用名词来构建路径，此时可以引入 action 命名。

```text
【PUT】  /v1/users/{user_id}/password/actions/modify // 密码修改
```

1

### [#](https://funtl.com/zh/spring-cloud-alibaba-myshop/理解-RESTful-风格的-API.html#请求方式)请求方式

可以通过 GET、 POST、 PUT、 PATCH、 DELETE 等方式对服务端的资源进行操作。其中：

- GET：用于查询资源
- POST：用于创建资源
- PUT：用于更新服务端的资源的全部信息
- PATCH：用于更新服务端的资源的部分信息
- DELETE：用于删除服务端的资源。

这里，使用“用户”的案例进行回顾通过 GET、 POST、 PUT、 PATCH、 DELETE 等方式对服务端的资源进行操作。

```text
【GET】          /users                # 查询用户信息列表
【GET】          /users/1001           # 查看某个用户信息
【POST】         /users                # 新建用户信息
【PUT】          /users/1001           # 更新用户信息(全部字段)
【PATCH】        /users/1001           # 更新用户信息(部分字段)
【DELETE】       /users/1001           # 删除用户信息
```

1
2
3
4
5
6

### [#](https://funtl.com/zh/spring-cloud-alibaba-myshop/理解-RESTful-风格的-API.html#查询参数)查询参数

RESTful API 接口应该提供参数，过滤返回结果。其中，offset 指定返回记录的开始位置。一般情况下，它会结合 limit 来做分页的查询，这里 limit 指定返回记录的数量。

```text
【GET】  /{version}/{resources}/{resource_id}?offset=0&limit=20
```

1

同时，orderby 可以用来排序，但仅支持单个字符的排序，如果存在多个字段排序，需要业务中扩展其他参数进行支持。

```text
【GET】  /{version}/{resources}/{resource_id}?orderby={field} [asc|desc]
```

1

为了更好地选择是否支持查询总数，我们可以使用 count 字段，count 表示返回数据是否包含总条数，它的默认值为 false。

```text
【GET】  /{version}/{resources}/{resource_id}?count=[true|false]
```

1

上面介绍的 offset、 limit、 orderby 是一些公共参数。此外，业务场景中还存在许多个性化的参数。我们来看一个例子。

```text
【GET】  /v1/categorys/{category_id}/apps/{app_id}?enable=[1|0]&os_type={field}&device_ids={field,field,…}
```

1

注意的是，不要过度设计，只返回用户需要的查询参数。此外，需要考虑是否对查询参数创建数据库索引以提高查询性能。

### [#](https://funtl.com/zh/spring-cloud-alibaba-myshop/理解-RESTful-风格的-API.html#状态码)状态码

使用适合的状态码很重要，而不应该全部都返回状态码 200，或者随便乱使用。这里，列举在实际开发过程中常用的一些状态码，以供参考。

| 状态码 | 描述           |
| ------ | -------------- |
| 200    | 请求成功       |
| 201    | 创建成功       |
| 400    | 错误的请求     |
| 401    | 未验证         |
| 403    | 被拒绝         |
| 404    | 无法找到       |
| 409    | 资源冲突       |
| 500    | 服务器内部错误 |

### [#](https://funtl.com/zh/spring-cloud-alibaba-myshop/理解-RESTful-风格的-API.html#异常响应)异常响应

当 RESTful API 接口出现非 2xx 的 HTTP 错误码响应时，采用全局的异常结构响应信息。

```text
HTTP/1.1 400 Bad Request
Content-Type: application/json
{
    "code": "INVALID_ARGUMENT",
    "message": "{error message}",
    "cause": "{cause message}",
    "request_id": "01234567-89ab-cdef-0123-456789abcdef",
    "host_id": "{server identity}",
    "server_time": "2014-01-01T12:00:00Z"
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

### [#](https://funtl.com/zh/spring-cloud-alibaba-myshop/理解-RESTful-风格的-API.html#请求参数)请求参数

在设计服务端的 RESTful API 的时候，我们还需要对请求参数进行限制说明。例如一个支持批量查询的接口，我们要考虑最大支持查询的数量。

```text
【GET】     /v1/users/batch?user_ids=1001,1002      // 批量查询用户信息
参数说明
- user_ids: 用户ID串，最多允许 20 个。
```

1
2
3

此外，在设计新增或修改接口时，我们还需要在文档中明确告诉调用者哪些参数是必填项，哪些是选填项，以及它们的边界值的限制。

```text
【POST】     /v1/users                             // 创建用户信息
请求内容
{
    "username": "lusifer",                 // 必填, 用户名称, max 10
    "realname": "鲁斯菲尔",               // 必填, 用户名称, max 10
    "password": "123456",              // 必填, 用户密码, max 32
    "email": "topsale@vip.qq.com",     // 选填, 电子邮箱, max 32
    "weixin": "Lusifer",            // 选填，微信账号, max 32
    "sex": 1                           // 必填, 用户性别[1-男 2-女 99-未知]
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

### [#](https://funtl.com/zh/spring-cloud-alibaba-myshop/理解-RESTful-风格的-API.html#响应参数)响应参数

针对不同操作，服务端向用户返回的结果应该符合以下规范。

```text
【GET】     /{version}/{resources}/{resource_id}      // 返回单个资源对象
【GET】     /{version}/{resources}                    // 返回资源对象的列表
【POST】    /{version}/{resources}                    // 返回新生成的资源对象
【PUT】     /{version}/{resources}/{resource_id}      // 返回完整的资源对象
【PATCH】   /{version}/{resources}/{resource_id}      // 返回完整的资源对象
【DELETE】  /{version}/{resources}/{resource_id}      // 状态码 200，返回完整的资源对象。
                                                      // 状态码 204，返回一个空文档
```

1
2
3
4
5
6
7

如果是单条数据，则返回一个对象的 JSON 字符串。

```text
HTTP/1.1 200 OK
{
    "id" : "01234567-89ab-cdef-0123-456789abcdef",
    "name" : "example",
    "created_time": 1496676420000,
    "updated_time": 1496676420000,
    ...
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

如果是列表数据，则返回一个封装的结构体。

```text
HTTP/1.1 200 OK
{
    "count":100,
    "items":[
        {
            "id" : "01234567-89ab-cdef-0123-456789abcdef",
            "name" : "example",
            "created_time": 1496676420000,
            "updated_time": 1496676420000,
            ...
        },
        ...
    ]
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

### [#](https://funtl.com/zh/spring-cloud-alibaba-myshop/理解-RESTful-风格的-API.html#一个完整的案例)一个完整的案例

最后，我们使用一个完整的案例将前面介绍的知识整合起来。这里，使用“获取用户列表”的案例。

```text
【GET】     /v1/users?[&keyword=xxx][&enable=1][&offset=0][&limit=20] 获取用户列表
功能说明：获取用户列表
请求方式：GET
参数说明
- keyword: 模糊查找的关键字。[选填]
- enable: 启用状态[1-启用 2-禁用]。[选填]
- offset: 获取位置偏移，从 0 开始。[选填]
- limit: 每次获取返回的条数，缺省为 20 条，最大不超过 100。 [选填]
响应内容
HTTP/1.1 200 OK
{
    "count":100,
    "items":[
        {
            "id" : "01234567-89ab-cdef-0123-456789abcdef",
            "name" : "example",
            "created_time": 1496676420000,
            "updated_time": 1496676420000,
            ...
        },
        ...
    ]
}
失败响应
HTTP/1.1 403 UC/AUTH_DENIED
Content-Type: application/json
{
    "code": "INVALID_ARGUMENT",
    "message": "{error message}",
    "cause": "{cause message}",
    "request_id": "01234567-89ab-cdef-0123-456789abcdef",
    "host_id": "{server identity}",
    "server_time": "2014-01-01T12:00:00Z"
}
错误代码
- 403 UC/AUTH_DENIED    授权受限
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

## [#](https://funtl.com/zh/spring-cloud-alibaba-myshop/理解-RESTful-风格的-API.html#怎么理解-restful-api-的幂等性)怎么理解 RESTful API 的幂等性

HTTP 幂等方法，是指无论调用多少次都不会有不同结果的 HTTP 方法。不管你调用一次，还是调用一百次，一千次，结果都是相同的。

```text
GET     /tickets       # 获取ticket列表
GET     /tickets/12    # 查看某个具体的ticket
POST    /tickets       # 新建一个ticket
PUT     /tickets/12    # 更新ticket 12
PATCH   /tickets/12    # 更新ticket 12
DELETE  /tickets/12    # 删除ticekt 12
```

1
2
3
4
5
6

### [#](https://funtl.com/zh/spring-cloud-alibaba-myshop/理解-RESTful-风格的-API.html#http-get-方法)HTTP GET 方法

HTTP GET 方法，用于获取资源，不管调用多少次接口，结果都不会改变，所以是幂等的。

```text
GET     /tickets       # 获取ticket列表
GET     /tickets/12    # 查看某个具体的ticket
```

1
2

只是查询数据，不会影响到资源的变化，因此我们认为它幂等。

值得注意，幂等性指的是作用于结果而非资源本身。怎么理解呢？例如，这个 HTTP GET 方法可能会每次得到不同的返回内容，但并不影响资源。

可能你会问有这种情况么？当然有咯。例如，我们有一个接口获取当前时间，我们就应该设计成

```text
GET     /service_time # 获取服务器当前时间
```

1

它本身不会对资源本身产生影响，因此满足幂等性。

### [#](https://funtl.com/zh/spring-cloud-alibaba-myshop/理解-RESTful-风格的-API.html#http-post-方法)HTTP POST 方法

HTTP POST 方法是一个非幂等方法，因为调用多次，都将产生新的资源。

```text
POST    /tickets       # 新建一个ticket
```

1

因为它会对资源本身产生影响，每次调用都会有新的资源产生，因此不满足幂等性。

### [#](https://funtl.com/zh/spring-cloud-alibaba-myshop/理解-RESTful-风格的-API.html#http-put-方法)HTTP PUT 方法

HTTP PUT 方法是不是幂等的呢？我们来看下

```text
PUT     /tickets/12    # 更新ticket 12
```

1

因为它直接把实体部分的数据替换到服务器的资源，我们多次调用它，只会产生一次影响，但是有相同结果的 HTTP 方法，所以满足幂等性。

### [#](https://funtl.com/zh/spring-cloud-alibaba-myshop/理解-RESTful-风格的-API.html#http-patch-方法)HTTP PATCH 方法

HTTP PATCH 方法是非幂等的。HTTP POST 方法和 HTTP PUT 方法可能比较好理解，但是 HTTP PATCH 方法只是更新部分资源，怎么是非幂等的呢?

因为，PATCH 提供的实体则需要根据程序或其它协议的定义，解析后在服务器上执行，以此来修改服务器上的资源。换句话说，PATCH 请求是会执行某个程序的，如果重复提交，程序可能执行多次，对服务器上的资源就可能造成额外的影响，这就可以解释它为什么是非幂等的了。

可能你还不能理解这点。我们举个例子

```text
PATCH   /tickets/12    # 更新ticket 12
```

1

此时，我们服务端对方法的处理是，当调用一次方法，更新部分字段，将这条 ticket 记录的操作记录加一，这次，每次调用的资源是不是变了呢，所以它是有可能是非幂等的操作。

### [#](https://funtl.com/zh/spring-cloud-alibaba-myshop/理解-RESTful-风格的-API.html#http-delete-方法)HTTP DELETE 方法

HTTP DELETE 方法用于删除资源，会将资源删除。

```text
DELETE  /tickets/12    # 删除ticekt 12
```

1

调用一次和多次对资源产生影响是相同的，所以也满足幂等性。

### [#](https://funtl.com/zh/spring-cloud-alibaba-myshop/理解-RESTful-风格的-API.html#如何设计符合幂等性的高质量-restful-api)如何设计符合幂等性的高质量 RESTful API

#### [#](https://funtl.com/zh/spring-cloud-alibaba-myshop/理解-RESTful-风格的-API.html#http-get-vs-http-post)HTTP GET vs HTTP POST

也许，你会想起一个面试题。**HTTP 请求的 GET 与 POST 方式有什么区别？** 你可能会回答到：GET 方式通过 URL 提交数据，数据在 URL 中可以看到；POST 方式，数据放置在 HTML HEADER 内提交。但是，我们现在从 RESTful 的资源角度来看待问题，HTTP GET 方法是幂等的，所以它适合作为查询操作，HTTP POST 方法是非幂等的，所以用来表示新增操作。

但是，也有例外，我们有的时候可能需要把查询方法改造成 HTTP POST 方法。比如，超长（1k）的 GET URL 使用 POST 方法来替代，因为 GET 受到 URL 长度的限制。虽然，它不符合幂等性，但是它是一种折中的方案。

#### [#](https://funtl.com/zh/spring-cloud-alibaba-myshop/理解-RESTful-风格的-API.html#http-post-vs-http-put)HTTP POST vs HTTP PUT

对于 HTTP POST 方法和 HTTP PUT 方法，我们一般的理解是 POST 表示创建资源，PUT 表示更新资源。当然，这个是正确的理解。

但是，实际上，两个方法都用于创建资源，更为本质的差别是在幂等性。HTTP POST 方法是非幂等，所以用来表示创建资源，HTTP PUT 方法是幂等的，因此表示更新资源更加贴切。

#### [#](https://funtl.com/zh/spring-cloud-alibaba-myshop/理解-RESTful-风格的-API.html#http-put-vs-http-patch)HTTP PUT vs HTTP PATCH

此时，你看会有另外一个问题。HTTP PUT 方法和 HTTP PATCH 方法，都是用来表述更新资源，它们之间有什么区别呢？我们一般的理解是 PUT 表示更新全部资源，PATCH 表示更新部分资源。首先，这个是我们遵守的第一准则。根据上面的描述，PATCH 方法是非幂等的，因此我们在设计我们服务端的 RESTful API 的时候，也需要考虑。如果，我们想要明确的告诉调用者我们的资源是幂等的，我的设计更倾向于使用 HTTP PUT 方法。

上次更新: 2019-1-23 5:27:13

← [创建用户注册服务](https://funtl.com/zh/spring-cloud-alibaba-myshop/创建用户注册服务.html)[再谈 RESTful 风格的 API ](https://funtl.com/zh/spring-cloud-alibaba-myshop/再谈-RESTful-风格的-API.html)→