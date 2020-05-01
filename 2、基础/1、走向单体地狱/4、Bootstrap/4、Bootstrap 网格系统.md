# [#](https://funtl.com/zh/bootstrap/Bootstrap-网格系统.html#bootstrap-网格系统)Bootstrap 网格系统

## [#](https://funtl.com/zh/bootstrap/Bootstrap-网格系统.html#本节视频)本节视频

- [【视频】使用 Bootstrap-Bootstrap 网格布局](https://www.bilibili.com/video/av24482487/)

## [#](https://funtl.com/zh/bootstrap/Bootstrap-网格系统.html#概述)概述

Bootstrap 提供了一套响应式、移动设备优先的流式网格系统，随着屏幕或视口（viewport）尺寸的增加，系统会自动分为最多 12 列。

## [#](https://funtl.com/zh/bootstrap/Bootstrap-网格系统.html#什么是网格（grid）？)什么是网格（Grid）？

> 在平面设计中，网格是一种由一系列用于组织内容的相交的直线（垂直的、水平的）组成的结构（通常是二维的）。它广泛应用于打印设计中的设计布局和内容结构。在网页设计中，它是一种用于快速创建一致的布局和有效地使用 HTML 和 CSS 的方法。

简单地说，网页设计中的网格用于组织内容，让网站易于浏览，并降低用户端的负载。

## [#](https://funtl.com/zh/bootstrap/Bootstrap-网格系统.html#什么是-bootstrap-网格系统（grid-system）？)什么是 Bootstrap 网格系统（Grid System）？

Bootstrap 官方文档中有关网格系统的描述：

> Bootstrap 包含了一个响应式的、移动设备优先的、不固定的网格系统，可以随着设备或视口大小的增加而适当地扩展到 12 列。它包含了用于简单的布局选项的预定义类，也包含了用于生成更多语义布局的功能强大的混合类。

让我们来理解一下上面的语句。Bootstrap 3 是移动设备优先的，在这个意义上，Bootstrap 代码从小屏幕设备（比如移动设备、平板电脑）开始，然后扩展到大屏幕设备（比如笔记本电脑、台式电脑）上的组件和网格。

### [#](https://funtl.com/zh/bootstrap/Bootstrap-网格系统.html#移动设备优先策略)移动设备优先策略

- 内容
  - 决定什么是最重要的
  - 布局
- 优先设计更小的宽度
  - 基础的 CSS 是移动设备优先，**媒体查询** 是针对于平板电脑、台式电脑
  - 渐进增强
- 随着屏幕大小的增加而添加元素

响应式网格系统随着屏幕或视口（viewport）尺寸的增加，系统会自动分为最多 12 列。

![img](https://funtl.com/assets/Lusifer1528226157.png)

## [#](https://funtl.com/zh/bootstrap/Bootstrap-网格系统.html#bootstrap-网格系统（grid-system）的工作原理)Bootstrap 网格系统（Grid System）的工作原理

网格系统通过一系列包含内容的行和列来创建页面布局。下面列出了 Bootstrap 网格系统是如何工作的：

- 行必须放置在 `.container` class 内，以便获得适当的对齐（alignment）和内边距（padding）
- 使用行来创建列的水平组
- 内容应该放置在列内，且唯有列可以是行的直接子元素
- 预定义的网格类，比如 `.row` 和 `.col-xs-4`，可用于快速创建网格布局。LESS 混合类可用于更多语义布局
- 列通过内边距（padding）来创建列内容之间的间隙。该内边距是通过 `.rows` 上的外边距（margin）取负，表示第一列和最后一列的行偏移
- 网格系统是通过指定您想要横跨的十二个可用的列来创建的。例如，要创建三个相等的列，则使用三个 `.col-xs-4`

## [#](https://funtl.com/zh/bootstrap/Bootstrap-网格系统.html#网格选项)网格选项

下表总结了 Bootstrap 网格系统如何跨多个设备工作：

| 说明         | 超小设备手机（< 768px）       | 小型设备平板电脑（≥ 768px）   | 中型设备台式电脑（≥ 992px）   | 大型设备台式电脑（≥ 1200px）  |
| ------------ | ----------------------------- | ----------------------------- | ----------------------------- | ----------------------------- |
| 网格行为     | 一直是水平的                  | 以折叠开始，断点以上是水平的  | 以折叠开始，断点以上是水平的  | 以折叠开始，断点以上是水平的  |
| 最大容器宽度 | None (auto)                   | 750px                         | 970px                         | 1170px                        |
| Class 前缀   | `.col-xs-`                    | `.col-sm-`                    | `.col-md-`                    | `.col-lg-`                    |
| 列数量和     | 12                            | 12                            | 12                            | 12                            |
| 最大列宽     | Auto                          | 60px                          | 78px                          | 95px                          |
| 间隙宽度     | 30px（一个列的每边分别 15px） | 30px（一个列的每边分别 15px） | 30px（一个列的每边分别 15px） | 30px（一个列的每边分别 15px） |
| 可嵌套       | Yes                           | Yes                           | Yes                           | Yes                           |
| 偏移量       | Yes                           | Yes                           | Yes                           | Yes                           |
| 列排序       | Yes                           | Yes                           | Yes                           | Yes                           |

上次更新: 2019-4-19 19:23:15