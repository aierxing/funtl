# [#](https://funtl.com/zh/supplement2/nth-tabs.html#nth-tabs)nth-tabs

## [#](https://funtl.com/zh/supplement2/nth-tabs.html#什么是-nth-tabs)什么是 nth-tabs ?

基于 Bootstrap 的多功能选项卡插件

## [#](https://funtl.com/zh/supplement2/nth-tabs.html#其他依赖)其他依赖

- 滚动条：jquery.scrollbar
- 字体图标：font-awesome

## [#](https://funtl.com/zh/supplement2/nth-tabs.html#使用说明)使用说明

### [#](https://funtl.com/zh/supplement2/nth-tabs.html#css)CSS

```text
<link href="https://cdn.bootcss.com/bootstrap/3.3.5/css/bootstrap.min.css" rel="stylesheet">
<link href="https://cdn.bootcss.com/font-awesome/4.4.0/css/font-awesome.min.css" rel="stylesheet">
<link href="https://cdn.bootcss.com/jquery.scrollbar/0.2.11/jquery.scrollbar.min.css" rel="stylesheet">
<link href="css/nth.tabs.min.css" rel="stylesheet">
```

1
2
3
4

### [#](https://funtl.com/zh/supplement2/nth-tabs.html#js)JS

```text
<script src="https://cdn.bootcss.com/jquery/2.1.4/jquery.min.js"></script>
<script src="https://cdn.bootcss.com/bootstrap/3.3.5/js/bootstrap.min.js"></script>
<script src="https://cdn.bootcss.com/jquery.scrollbar/0.2.11/jquery.scrollbar.min.js"></script>
<script src="js/nth.tabs.min.js"></script>
```

1
2
3
4

### [#](https://funtl.com/zh/supplement2/nth-tabs.html#html)html

```text
<div class="nth-tabs" id="custom-id"></div>
```

1

### [#](https://funtl.com/zh/supplement2/nth-tabs.html#初始化)初始化

```text
nthTabs = $("#custom-id").nthTabs();
```

1

### [#](https://funtl.com/zh/supplement2/nth-tabs.html#添加一个选项卡)添加一个选项卡

```text
nthTabs.addTab({
 id:'a',
 title:'孙悟空',
 content:'看我七十二变',
});
```

1
2
3
4
5

### [#](https://funtl.com/zh/supplement2/nth-tabs.html#添加一个不可关闭的选项卡)添加一个不可关闭的选项卡

```text
nthTabs.addTab({
  id:'a',
  title:'孙悟空',
  content:'看我七十二变',
  allowClose:false,
});
```

1
2
3
4
5
6

### [#](https://funtl.com/zh/supplement2/nth-tabs.html#添加多个选项卡)添加多个选项卡

```text
nthTabs.addTab({
        id:'a',
        title:'孙悟空',
        content:'看我七十二变',
}).addTab({
        id:'b',
        title:'孙悟空二号',
        content:'看我七十三变',
});
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

### [#](https://funtl.com/zh/supplement2/nth-tabs.html#删除一个选项卡)删除一个选项卡

```text
nthTabs.delTab('id');
```

1

### [#](https://funtl.com/zh/supplement2/nth-tabs.html#删除其他选项卡)删除其他选项卡

```text
nthTabs.delOtherTab();
```

1

### [#](https://funtl.com/zh/supplement2/nth-tabs.html#删除所有选项卡)删除所有选项卡

```text
nthTabs.delAllTab();
```

1

### [#](https://funtl.com/zh/supplement2/nth-tabs.html#切换到指定选项卡)切换到指定选项卡

```text
nthTabs.setActTab(id);
```

1

### [#](https://funtl.com/zh/supplement2/nth-tabs.html#定位到当前选项卡)定位到当前选项卡

```text
nthTabs.locationTab();
```

1

### [#](https://funtl.com/zh/supplement2/nth-tabs.html#左滑动)左滑动

```text
$('.roll-nav-left').click();
```

1

### [#](https://funtl.com/zh/supplement2/nth-tabs.html#右滑动)右滑动

```text
$('.roll-nav-right').click();
```

1

### [#](https://funtl.com/zh/supplement2/nth-tabs.html#获取左右滑动步值)获取左右滑动步值

```text
nthTabs.getMarginStep();
```

1

### [#](https://funtl.com/zh/supplement2/nth-tabs.html#获取当前选项卡id)获取当前选项卡ID

```text
nthTabs.getActiveId();
```

1

### [#](https://funtl.com/zh/supplement2/nth-tabs.html#获取所有选项卡宽度)获取所有选项卡宽度

```text
nthTabs.getAllTabWidth();
```

1

### [#](https://funtl.com/zh/supplement2/nth-tabs.html#获取所有选项卡)获取所有选项卡

```text
nthTabs.nthTabs.getTabList();
```

1

## [#](https://funtl.com/zh/supplement2/nth-tabs.html#附：群里提供的版本)附：群里提供的版本

群里提供的版本是为了适应 AdminLTE 而修改的版本，使用方式略有不同

### [#](https://funtl.com/zh/supplement2/nth-tabs.html#css-2)CSS

```text
<link href="https://cdn.bootcss.com/bootstrap/3.3.5/css/bootstrap.min.css" rel="stylesheet">
<link href="https://cdn.bootcss.com/font-awesome/4.4.0/css/font-awesome.min.css" rel="stylesheet">
<link href="https://cdn.bootcss.com/jquery.scrollbar/0.2.11/jquery.scrollbar.min.css" rel="stylesheet">
<link href="css/nth.tabs.css" rel="stylesheet">
```

1
2
3
4

主要修改了 `nth.tabs.css` 部分样式以适应 AdminLTE

### [#](https://funtl.com/zh/supplement2/nth-tabs.html#js-2)JS

```text
<script src="https://cdn.bootcss.com/jquery/2.1.4/jquery.min.js"></script>
<script src="https://cdn.bootcss.com/bootstrap/3.3.5/js/bootstrap.min.js"></script>
<script src="https://cdn.bootcss.com/jquery.scrollbar/0.2.11/jquery.scrollbar.min.js"></script>
<script src="js/nth.tabs.min.js"></script>
<script src="js/nth-tabs.js"></script>
```

1
2
3
4
5

主要增加了 `nth-tabs.js` 这个二次封装的自定义工具类

### [#](https://funtl.com/zh/supplement2/nth-tabs.html#html-2)html

```text
<div class="nth-tabs common-bg" id="editor-tabs"></div>
```

1

### [#](https://funtl.com/zh/supplement2/nth-tabs.html#初始化-2)初始化

```text
$(function () {
    NthTabs.home("/path");
});
```

1
2
3

### [#](https://funtl.com/zh/supplement2/nth-tabs.html#添加一个选项卡-2)添加一个选项卡

```text
NthTabs.addTab('id', 'name', '/path');
```

1

上次更新: 2018-12-31 18:51:48

← [HttpServletUtils](https://funtl.com/zh/supplement2/)[什么是跨域问题 ](https://funtl.com/zh/supplement2/什么是跨域问题.html)→