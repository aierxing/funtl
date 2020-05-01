# Thymeleaf 模板布局

这节主要介绍模板的引入。及如何在不改变前端人员的 HTML 显示结果的情况下设计模板（通过属性配置动态时不显示的部分）。

## [#](https://funtl.com/zh/spring-boot-thymeleaf/Thymeleaf-模板布局.html#模板模块导入)模板模块导入

首先定义一个 `/resources/templates/footer.html` 文件：

```text
<!DOCTYPE html SYSTEM "http://www.thymeleaf.org/dtd/xhtml1-strict-thymeleaf-4.dtd">
<html xmlns="http://www.w3.org/1999/xhtml" xmlns:th="http://www.thymeleaf.org">
    <body>
        <div th:fragment="copy">
            &copy; 2018 Copyright by Lusifer.
        </div>
    </body>
</html>
```

1
2
3
4
5
6
7
8

上面的代码定义了一个片段称为 `copy`，我们可以很容易地使用 `th:include` 或者 `th:replace` 属性包含在我们的主页上

```text
<body>
...
<div th:include="footer :: copy"></div>
</body>
```

1
2
3
4

`include` 的表达式想当简洁。这里有三种写法：

- `templatename::domselector` 或者 `templatename::[domselector]` 引入模板页面中的某个模块
- `templatename` 引入模板页面
- `::domselector` 或者 `this::domselector` 引入自身模板的模块

上面所有的 `templatename` 和 `domselector` 的写法都支持表达式写法：

```text
<div th:include="footer :: (${user.isAdmin}? #{footer.admin} : #{footer.normaluser})"></div>
```

1

## [#](https://funtl.com/zh/spring-boot-thymeleaf/Thymeleaf-模板布局.html#不使用-th-fragment-来引用模块)不使用 `th:fragment` 来引用模块

```text
<div id="copy-section">
&copy; 2018 Copyright by Lusifer.
</div>
```

1
2
3

我们可以用 CSS 的选择器写法来引入

```text
<body>
...
<div th:include="footer :: #copy-section"></div>
</body>
```

1
2
3
4

## [#](https://funtl.com/zh/spring-boot-thymeleaf/Thymeleaf-模板布局.html#th-include-和-th-replace-的区别)`th:include` 和 `th:replace` 的区别

`th:include` 和 `th:replace` 都可以引入模块，两者的区别在于：

- `th:include`：引入子模块的 children，依然保留父模块的 tag
- `th:replace`：引入子模块的所有，不保留父模块的 tag

### [#](https://funtl.com/zh/spring-boot-thymeleaf/Thymeleaf-模板布局.html#举个例子)举个例子

```text
<footer th:fragment="copy">
&copy; 2018 Copyright by Lusifer.
</footer>
```

1
2
3

### [#](https://funtl.com/zh/spring-boot-thymeleaf/Thymeleaf-模板布局.html#引入界面)引入界面

```text
<body>
...
<div th:include="footer :: copy"></div>
<div th:replace="footer :: copy"></div>
</body>
```

1
2
3
4
5

### [#](https://funtl.com/zh/spring-boot-thymeleaf/Thymeleaf-模板布局.html#显示结果)显示结果

```text
<body>
...
<div>
&copy; 2018 Copyright by Lusifer.
</div>
<footer>
&copy; 2018 Copyright by Lusifer.
</footer>
</body>
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

上次更新: 2018-12-31 18:51:48

← [Thymeleaf 内置对象](https://funtl.com/zh/spring-boot-thymeleaf/Thymeleaf-内置对象.html)