# [#](https://funtl.com/zh/spring-cloud-alibaba-myshop/实现-RESTful-风格的-API.html#实现-restful-风格的-api)实现 RESTful 风格的 API

## [#](https://funtl.com/zh/spring-cloud-alibaba-myshop/实现-RESTful-风格的-API.html#本节视频)本节视频

- [【视频】Spring Cloud Alibaba-MyShop-实现 RESTful 风格 API](https://www.bilibili.com/video/av41525784/)

**注意：本节视频时间较长，但包含了很多基础性的操作，有兴趣的同学不要错过** (๑´ㅂ`๑́)و✧

## [#](https://funtl.com/zh/spring-cloud-alibaba-myshop/实现-RESTful-风格的-API.html#本节关键代码)本节关键代码

### [#](https://funtl.com/zh/spring-cloud-alibaba-myshop/实现-RESTful-风格的-API.html#pom)POM

主要在 `myshop-commons` 项目增加了如下依赖：

```xml
<dependency>
    <groupId>com.fasterxml.jackson.core</groupId>
    <artifactId>jackson-databind</artifactId>
</dependency>
<dependency>
    <groupId>com.github.ben-manes.caffeine</groupId>
    <artifactId>guava</artifactId>
</dependency>
<dependency>
    <groupId>org.apache.commons</groupId>
    <artifactId>commons-lang3</artifactId>
</dependency>
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

### [#](https://funtl.com/zh/spring-cloud-alibaba-myshop/实现-RESTful-风格的-API.html#相关工具类)相关工具类

主要在 `myshop-commons` 项目中增加了如下工具类：

#### [#](https://funtl.com/zh/spring-cloud-alibaba-myshop/实现-RESTful-风格的-API.html#abstractbaseresult)AbstractBaseResult

```java
package com.funtl.myshop.commons.dto;

import com.fasterxml.jackson.annotation.JsonInclude;
import lombok.Data;

import java.io.Serializable;

/**
 * 通用的响应结果
 * <p>Title: AbstractBaseResult</p>
 * <p>Description: </p>
 *
 * @author Lusifer
 * @version 1.0.0
 * @date 2019/1/23 15:10
 */
@Data
public abstract class AbstractBaseResult implements Serializable {

    @Data
    @JsonInclude(JsonInclude.Include.NON_NULL)
    protected static class Links {
        private String self;
        private String next;
        private String last;
    }

    @Data
    @JsonInclude(JsonInclude.Include.NON_NULL)
    protected static class DataBean<T extends AbstractBaseDomain> {
        private String type;
        private Long id;
        private T attributes;
        private T relationships;
        private Links links;
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

#### [#](https://funtl.com/zh/spring-cloud-alibaba-myshop/实现-RESTful-风格的-API.html#abstractbasedomain)AbstractBaseDomain

```java
package com.funtl.myshop.commons.dto;

import lombok.Data;

import java.io.Serializable;

/**
 * 通用的领域模型
 * <p>Title: AbstractBaseDomain</p>
 * <p>Description: </p>
 *
 * @author Lusifer
 * @version 1.0.0
 * @date 2019/1/23 15:50
 */
@Data
public abstract class AbstractBaseDomain implements Serializable {
    private Long id;
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

#### [#](https://funtl.com/zh/spring-cloud-alibaba-myshop/实现-RESTful-风格的-API.html#successresult)SuccessResult

```java
package com.funtl.myshop.commons.dto;

import com.google.common.collect.Lists;
import lombok.Data;
import lombok.EqualsAndHashCode;
import org.apache.commons.lang3.StringUtils;

import java.util.List;

/**
 * 请求成功
 * <p>Title: SuccessResult</p>
 * <p>Description: </p>
 *
 * @author Lusifer
 * @version 1.0.0
 * @date 2019/1/23 15:07
 */
@Data
@EqualsAndHashCode(callSuper = false)
public class SuccessResult<T extends AbstractBaseDomain> extends AbstractBaseResult {
    private Links links;
    private List<DataBean> data;

    public SuccessResult(String self, T attributes) {
        links = new Links();
        links.setSelf(self);

        createDataBean(null, attributes);
    }

    public SuccessResult(String self, int next, int last, List<T> attributes) {
        links = new Links();
        links.setSelf(self);
        links.setNext(self + "?page=" + next);
        links.setLast(self + "?page=" + last);

        attributes.forEach(attribute -> createDataBean(self, attribute));
    }

    private void createDataBean(String self, T attributes) {
        if (data == null) {
            data = Lists.newArrayList();
        }

        DataBean dataBean = new DataBean();
        dataBean.setId(attributes.getId());
        dataBean.setType(attributes.getClass().getSimpleName());
        dataBean.setAttributes(attributes);

        if (StringUtils.isNotBlank(self)) {
            Links links = new Links();
            links.setSelf(self + "/" + attributes.getId());
            dataBean.setLinks(links);
        }

        data.add(dataBean);
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

#### [#](https://funtl.com/zh/spring-cloud-alibaba-myshop/实现-RESTful-风格的-API.html#errorresult)ErrorResult

```java
package com.funtl.myshop.commons.dto;

import com.fasterxml.jackson.annotation.JsonInclude;
import lombok.AllArgsConstructor;
import lombok.Data;
import lombok.EqualsAndHashCode;

/**
 * 请求失败
 * <p>Title: ErrorResult</p>
 * <p>Description: </p>
 *
 * @author Lusifer
 * @version 1.0.0
 * @date 2019/1/23 15:07
 */
@Data
@AllArgsConstructor
@EqualsAndHashCode(callSuper = false)
@JsonInclude(JsonInclude.Include.NON_NULL)
public class ErrorResult extends AbstractBaseResult {
    private int code;
    private String title;
    private String detail;
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

#### [#](https://funtl.com/zh/spring-cloud-alibaba-myshop/实现-RESTful-风格的-API.html#baseresultfactory)BaseResultFactory

```java
package com.funtl.myshop.commons.dto;

import java.util.List;

/**
 * 通用响应结构工厂
 * <p>Title: BaseResultFactory</p>
 * <p>Description: </p>
 *
 * @author Lusifer
 * @version 1.0.0
 * @date 2019/1/23 15:16
 */
public class BaseResultFactory<T extends AbstractBaseDomain> {

    private static final String LOGGER_LEVEL_DEBUG = "DEBUG";

    private static BaseResultFactory baseResultFactory;

    private BaseResultFactory() {

    }

    public static BaseResultFactory getInstance() {
        if (baseResultFactory == null) {
            synchronized (BaseResultFactory.class) {
                if (baseResultFactory == null) {
                    baseResultFactory = new BaseResultFactory();
                }
            }
        }
        return baseResultFactory;
    }

    /**
     * 构建单笔数据结果集
     *
     * @param self
     * @return
     */
    public AbstractBaseResult build(String self, T attributes) {
        return new SuccessResult(self, attributes);
    }

    /**
     * 构建多笔数据结果集
     *
     * @param self
     * @param next
     * @param last
     * @return
     */
    public AbstractBaseResult build(String self, int next, int last, List<T> attributes) {
        return new SuccessResult(self, next, last, attributes);
    }

    /**
     * 构建请求错误的响应结构
     *
     * @param code
     * @param title
     * @param detail
     * @param level  日志级别，只有 DEBUG 时才显示详情
     * @return
     */
    public AbstractBaseResult build(int code, String title, String detail, String level) {
        if (LOGGER_LEVEL_DEBUG.equals(level)) {
            return new ErrorResult(code, title, detail);
        } else {
            return new ErrorResult(code, title, null);
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

### [#](https://funtl.com/zh/spring-cloud-alibaba-myshop/实现-RESTful-风格的-API.html#testcontroller)TestController

```java
package com.funtl.myshop.service.reg.controller;

import com.funtl.myshop.commons.domain.TbUser;
import com.funtl.myshop.commons.dto.AbstractBaseResult;
import com.funtl.myshop.commons.dto.BaseResultFactory;
import com.google.common.collect.Lists;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.context.ConfigurableApplicationContext;
import org.springframework.http.HttpStatus;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

import javax.servlet.http.HttpServletRequest;
import java.util.List;

@RestController
@RequestMapping(value = "test")
public class TestController {

    @Autowired
    private ConfigurableApplicationContext applicationContext;

    @GetMapping(value = "records/{id}")
    public AbstractBaseResult getById(HttpServletRequest request, @PathVariable long id) {
        TbUser tbUser = new TbUser();
        tbUser.setId(1L);
        tbUser.setUsername("和谷桐人");

        if (id == 1) {
            return BaseResultFactory.getInstance().build(request.getRequestURI(), tbUser);
        } else {
            return BaseResultFactory.build(HttpStatus.UNAUTHORIZED.value(), "参数类型错误", "ID 只能为 1", applicationContext.getEnvironment().getProperty("logging.level.com.funtl.myshop"));
        }
    }

    @GetMapping(value = "records")
    public AbstractBaseResult getList(HttpServletRequest request) {
        TbUser tbUser1 = new TbUser();
        tbUser1.setId(1L);
        tbUser1.setUsername("和谷桐人");

        TbUser tbUser2 = new TbUser();
        tbUser2.setId(2L);
        tbUser2.setUsername("亚丝娜");

        List<TbUser> tbUsers = Lists.newArrayList();
        tbUsers.add(tbUser1);
        tbUsers.add(tbUser2);

        return BaseResultFactory.getInstance().build(request.getRequestURI(), 2, 10, tbUsers);
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

### [#](https://funtl.com/zh/spring-cloud-alibaba-myshop/实现-RESTful-风格的-API.html#设置-json-不返回-null-字段)设置 Json 不返回 null 字段

```java
@JsonInclude(JsonInclude.Include.NON_NULL)
```

1

## [#](https://funtl.com/zh/spring-cloud-alibaba-myshop/实现-RESTful-风格的-API.html#附：springmvc-返回状态码)附：SpringMVC 返回状态码

```java
response.setHeader("Content-Type", "application/vnd.api+json");
response.setStatus(500);
```

1
2

上次更新: 2019-1-25 8:46:01

← [再谈 RESTful 风格的 API](https://funtl.com/zh/spring-cloud-alibaba-myshop/再谈-RESTful-风格的-API.html)[完善用户注册服务 ](https://funtl.com/zh/spring-cloud-alibaba-myshop/完善用户注册服务.html)→