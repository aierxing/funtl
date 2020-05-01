# [#](https://funtl.com/zh/bootstrap/Bootstrap-表格.html#bootstrap-表格)Bootstrap 表格

## [#](https://funtl.com/zh/bootstrap/Bootstrap-表格.html#本节视频)本节视频

- [【视频】使用 Bootstrap-Bootstrap 表格](https://www.bilibili.com/video/av24482490/)

## [#](https://funtl.com/zh/bootstrap/Bootstrap-表格.html#概述)概述

Bootstrap 提供了一个清晰的创建表格的布局。下表列出了 Bootstrap 支持的一些表格元素：

| 标签        | 描述                                                         |
| ----------- | ------------------------------------------------------------ |
| `<table>`   | 为表格添加基础样式                                           |
| `<thead>`   | 表格标题行的容器元素（`<tr>`），用来标识表格列               |
| `<tbody>`   | 表格主体中的表格行的容器元素（`<tr>`）                       |
| `<tr>`      | 一组出现在单行上的表格单元格的容器元素（`<td>` 或 `<th>`）   |
| `<td>`      | 默认的表格单元格                                             |
| `<th>`      | 特殊的表格单元格，用来标识列或行（取决于范围和位置）。必须在`<thead`内使用 |
| `<caption>` | 关于表格存储内容的描述或总结                                 |

## [#](https://funtl.com/zh/bootstrap/Bootstrap-表格.html#表格类)表格类

下表样式可用于表格中：

| 类                 | 描述                                            |
| ------------------ | ----------------------------------------------- |
| `.table`           | 为任意 `<table>` 添加基本样式 (只有横向分隔线)  |
| `.table-striped`   | 在`<tbody>`内添加斑马线形式的条纹 ( IE8 不支持) |
| `.table-bordered`  | 为所有表格的单元格添加边框                      |
| `.table-hover`     | 在`<tbody>` 内的任一行启用鼠标悬停状态          |
| `.table-condensed` | 让表格更加紧凑                                  |

## [#](https://funtl.com/zh/bootstrap/Bootstrap-表格.html#tr-th-和-td-类)`<tr>`, `<th>` 和 `<td>` 类

下表的类可用于表格的行或者单元格：

| 类         | 描述                             |
| ---------- | -------------------------------- |
| `.active`  | 将悬停的颜色应用在行或者单元格上 |
| `.success` | 表示成功的操作                   |
| `.info`    | 表示信息变化的操作               |
| `.warning` | 表示一个警告的操作               |
| `.danger`  | 表示一个危险的操作               |

## [#](https://funtl.com/zh/bootstrap/Bootstrap-表格.html#基本的表格)基本的表格

如果您想要一个只带有内边距（padding）和水平分割的基本表，请添加 class `.table`，如下面实例所示：

```html
<table class="table">
  <caption>基本的表格布局</caption>
  <thead>
    <tr>
      <th>名称</th>
      <th>城市</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>Tanmay</td>
      <td>Bangalore</td>
    </tr>
    <tr>
      <td>Sachin</td>
      <td>Mumbai</td>
    </tr>
  </tbody>
</table>
```

上次更新: 2018-12-29 1:45:15