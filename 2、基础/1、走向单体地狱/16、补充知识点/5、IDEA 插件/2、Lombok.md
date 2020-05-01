# [#](https://funtl.com/zh/supplement1/Lombok-简化臃肿代码.html#lombok)Lombok

Lombok 是一个可以通过简单的注解形式来帮助我们简化消除一些必须有但显得很臃肿的 Java 代码的工具，通过使用对应的注解，可以在编译源码的时候生成对应的方法。

- 官网地址：https://projectlombok.org/
- GitHub：https://github.com/rzwitserloot/lombok

## [#](https://funtl.com/zh/supplement1/Lombok-简化臃肿代码.html#idea-安装-lombok-插件)IDEA 安装 Lombok 插件

IDEA 中依次点击 `File` --> `Settings` --> `Plugins` 搜索 Lombok 安装即可

![img](https://funtl.com/assets/Lusifer1512345603.png)

## [#](https://funtl.com/zh/supplement1/Lombok-简化臃肿代码.html#查看是否安装成功)查看是否安装成功

![img](https://funtl.com/assets/Lusifer1512345786.png)

## [#](https://funtl.com/zh/supplement1/Lombok-简化臃肿代码.html#使用-lombok)使用 Lombok

### [#](https://funtl.com/zh/supplement1/Lombok-简化臃肿代码.html#pom)POM

`pom.xml` 中增加所需依赖，坐标如下：

```xml
<dependency>
    <groupId>org.projectlombok</groupId>
    <artifactId>lombok</artifactId>
    <version>1.16.18</version>
</dependency>
```

### [#](https://funtl.com/zh/supplement1/Lombok-简化臃肿代码.html#使用-data-注解简化-pojo)使用 `@Data` 注解简化 POJO

`@Data` 包含了 `@ToString`，`@EqualsAndHashCode`，`@Getter/@Setter` 和 `@RequiredArgsConstructor` 的功能

其他相关注解请自行查阅：http://jnb.ociweb.com/jnb/jnbJan2010.html

### [#](https://funtl.com/zh/supplement1/Lombok-简化臃肿代码.html#使用案例)使用案例

```java
@Data
public class ItemCatNode implements Serializable {
    @JsonProperty(value = "u")
    private String url;
    @JsonProperty(value = "n")
    private String name;
    @JsonProperty(value = "i")
    private List<?> item;
}
```

![img](https://funtl.com/assets/Lusifer1512346835.png)

上次更新: 2019-4-19 19:23:15