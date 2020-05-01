# [#](https://funtl.com/zh/supplement1/使用-Google-Kaptcha-生成验证码.html#kaptcha)Kaptcha

## [#](https://funtl.com/zh/supplement1/使用-Google-Kaptcha-生成验证码.html#验证码的作用)验证码的作用

防止恶意破解密码、刷票、论坛灌水、刷页。

有效防止某个黑客对某一个特定注册用户用特定程序暴力破解方式进行不断的登录尝试，实际上使用验证码是现在很多网站通行的方式（比如招商银行的网上个人银行，百度社区），我们利用比较简易的方式实现了这个功能。虽然登录麻烦一点，但是对网友的密码安全来说这个功能还是很有必要，也很重要。但我们还是 提醒大家要保护好自己的密码 ，尽量使用混杂了数字、字母、符号在内的 6 位以上密码，不要使用诸如 1234 之类的简单密码或者与用户名相同、类似的密码 ，免得你的账号被人盗用给自己带来不必要的麻烦。

验证码通常使用一些线条和一些不规则的字符组成，主要作用是为了防止一些黑客把密码数据化盗取。

## [#](https://funtl.com/zh/supplement1/使用-Google-Kaptcha-生成验证码.html#kaptcha-简介)Kaptcha 简介

Kaptcha 是一个可高度配置的实用验证码生成工具，可自由配置的选项如：

- 验证码的字体
- 验证码字体的大小
- 验证码字体的字体颜色
- 验证码内容的范围(数字，字母，中文汉字！)
- 验证码图片的大小，边框，边框粗细，边框颜色
- 验证码的干扰线
- 验证码的样式(鱼眼样式、3D、普通模糊、...)

## [#](https://funtl.com/zh/supplement1/使用-Google-Kaptcha-生成验证码.html#kaptcha-详细配置表)Kaptcha 详细配置表

| Constant                         | 描述                                                         | 默认值                                                |
| -------------------------------- | ------------------------------------------------------------ | ----------------------------------------------------- |
| kaptcha.border                   | 图片边框，合法值：yes , no                                   | yes                                                   |
| kaptcha.border.color             | 边框颜色，合法值： r,g,b (and optional alpha) 或者 white,black,blue. | black                                                 |
| kaptcha.image.width              | 图片宽                                                       | 200                                                   |
| kaptcha.image.height             | 图片高                                                       | 50                                                    |
| kaptcha.producer.impl            | 图片实现类                                                   | com.google.code.kaptcha.impl.DefaultKaptcha           |
| kaptcha.textproducer.impl        | 文本实现类                                                   | com.google.code.kaptcha.text.impl.DefaultTextCreator  |
| kaptcha.textproducer.char.string | 文本集合，验证码值从此集合中获取                             | abcde2345678gfynmnpwx                                 |
| kaptcha.textproducer.char.length | 验证码长度                                                   | 5                                                     |
| kaptcha.textproducer.font.names  | 字体                                                         | Arial, Courier                                        |
| kaptcha.textproducer.font.size   | 字体大小                                                     | 40px.                                                 |
| kaptcha.textproducer.font.color  | 字体颜色，合法值： r,g,b 或者 white,black,blue.              | black                                                 |
| kaptcha.textproducer.char.space  | 文字间隔                                                     | 2                                                     |
| kaptcha.noise.impl               | 干扰实现类                                                   | com.google.code.kaptcha.impl.DefaultNoise             |
| kaptcha.noise.color              | 干扰 颜色，合法值： r,g,b 或者 white,black,blue.             | black                                                 |
| kaptcha.obscurificator.impl      | 图片样式： 水纹 com.google.code.kaptcha.impl.WaterRipple 鱼眼 com.google.code.kaptcha.impl.FishEyeGimpy 阴影 com.google.code.kaptcha.impl.ShadowGimpy | com.google.code.kaptcha.impl.WaterRipple              |
| kaptcha.background.impl          | 背景实现类                                                   | com.google.code.kaptcha.impl.DefaultBackground        |
| kaptcha.background.clear.from    | 背景颜色渐变，开始颜色                                       | light grey                                            |
| kaptcha.background.clear.to      | 背景颜色渐变， 结束颜色                                      | white                                                 |
| kaptcha.word.impl                | 文字渲染器                                                   | com.google.code.kaptcha.text.impl.DefaultWordRenderer |
| kaptcha.session.key              | session key                                                  | KAPTCHA_SESSION_KEY                                   |
| kaptcha.session.date             | session date                                                 | KAPTCHA_SESSION_DATE                                  |

## [#](https://funtl.com/zh/supplement1/使用-Google-Kaptcha-生成验证码.html#spring-mvc-整合-kaptcha)Spring MVC 整合 Kaptcha

### [#](https://funtl.com/zh/supplement1/使用-Google-Kaptcha-生成验证码.html#pom)POM

`pom.xml` 配置文件如下：

```xml
<dependency>
    <groupId>com.google.code.kaptcha</groupId>
    <artifactId>kaptcha</artifactId>
    <version>2.3</version>
</dependency>
```

主要增加了 `com.google.code.kaptcha:kaptcha` 依赖

### [#](https://funtl.com/zh/supplement1/使用-Google-Kaptcha-生成验证码.html#创建-spring-配置)创建 Spring 配置

创建一个名为 `spring-context-kaptcha.xml` Spring 配置文件，配置如下：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean id="captchaProducer" class="com.google.code.kaptcha.impl.DefaultKaptcha">
        <property name="config">
            <bean class="com.google.code.kaptcha.util.Config">
                <constructor-arg>
                    <props>
                        <prop key="kaptcha.border">yes</prop>
                        <prop key="kaptcha.border.color">105,179,90</prop>
                        <prop key="kaptcha.textproducer.font.color">blue</prop>
                        <prop key="kaptcha.image.width">125</prop>
                        <prop key="kaptcha.image.height">45</prop>
                        <prop key="kaptcha.textproducer.font.size">45</prop>
                        <prop key="kaptcha.session.key">code</prop>
                        <prop key="kaptcha.textproducer.char.length">4</prop>
                        <prop key="kaptcha.textproducer.font.names">宋体,楷体,微软雅黑</prop>
                    </props>
                </constructor-arg>
            </bean>
        </property>
    </bean>
</beans>
```

### [#](https://funtl.com/zh/supplement1/使用-Google-Kaptcha-生成验证码.html#控制器关键代码)控制器关键代码

`Controller` 层的关键代码如下，主要作用为将生成的验证码放入 `Session` 并输出到页面

```java
package com.funtl.my.shop.web.ui.controller;

import com.google.code.kaptcha.Constants;
import com.google.code.kaptcha.Producer;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestMethod;
import org.springframework.web.servlet.ModelAndView;

import javax.imageio.ImageIO;
import javax.servlet.ServletOutputStream;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.awt.image.BufferedImage;
import java.io.IOException;

@Controller
public class KaptchaController {

    @Autowired
    private Producer captchaProducer;

    @RequestMapping(value = "verification", method = RequestMethod.GET)
    public ModelAndView verification(HttpServletRequest request, HttpServletResponse response) throws IOException {
        response.setDateHeader("Expires", 0);
        // Set standard HTTP/1.1 no-cache headers.
        response.setHeader("Cache-Control", "no-store, no-cache, must-revalidate");
        // Set IE extended HTTP/1.1 no-cache headers (use addHeader).
        response.addHeader("Cache-Control", "post-check=0, pre-check=0");
        // Set standard HTTP/1.0 no-cache header.
        response.setHeader("Pragma", "no-cache");
        // return a jpeg
        response.setContentType("image/jpeg");
        // create the text for the image
        String capText = captchaProducer.createText();
        // store the text in the session
        request.getSession().setAttribute(Constants.KAPTCHA_SESSION_KEY, capText);
        // create the image with the text
        BufferedImage bi = captchaProducer.createImage(capText);
        ServletOutputStream out = response.getOutputStream();
        // write the data out
        ImageIO.write(bi, "jpg", out);
        try {
            out.flush();
        } finally {
            out.close();
        }
        return null;
    }
}
```

### [#](https://funtl.com/zh/supplement1/使用-Google-Kaptcha-生成验证码.html#jsp-关键代码)JSP 关键代码

JSP 使用 `` 标签去请求验证码图片

```html
<img id="verification" src="/verification" style="cursor: pointer;" title="看不清？换一张" />
```

为图片绑定一个点击事件用于无刷新更换验证码

```js
$(function () {
    // 刷新验证码
    $("#verification").bind("click", function () {
        $(this).hide().attr('src', '/verification?random=' + Math.random()).fadeIn();
    });
});
```

上次更新: 2018-12-29 5:44:48