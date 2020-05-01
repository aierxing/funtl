# [#](https://funtl.com/zh/supplement1/jQuery-TreeTable.html#jquery-treetable)jQuery TreeTable

treeTable 是跨浏览器、性能很高的 jQuery 的树表组件，它使用非常简单，只需要引用 jQuery 库和一个 js 文件，接口也很简单。

优点：

- 兼容主流浏览器：支持 IE6 和 IE6+, Firefox, chrome, Opera, Safari
- 接口简洁：在普通表格的基础上增加父子关系的自定义标签就可以
- 组件性能高：内部实现了只绑定了 table 的事件、使用了 css sprite 合并图片等
- 提供两种风格：通过参数来设置风格

## [#](https://funtl.com/zh/supplement1/jQuery-TreeTable.html#页面引用)页面引用

CSS 部分

```html
<link rel="stylesheet" href="/static/assets/plugins/treeTable/themes/vsStyle/treeTable.min.css" />
```

JS 部分

```html
<script src="/static/assets/plugins/treeTable/jquery.treeTable.min.js"></script>
```

## [#](https://funtl.com/zh/supplement1/jQuery-TreeTable.html#接口)接口

### [#](https://funtl.com/zh/supplement1/jQuery-TreeTable.html#配置参数)配置参数

- theme: string 主题，有两个选项：default、vsStyle. 默认:default
- expandLevel: int 树表的展开层次. 默认:1
- column: int 可控制列的序号. 默认:0，即第一列
- onSelect: function 拥有 controller 自定义属性的元素的点击事件，return false 则中止展开
- beforeExpand: 展开子节点前触发的事件

### [#](https://funtl.com/zh/supplement1/jQuery-TreeTable.html#属性说明)属性说明

- id: string 行的 id
- pId: string 父行的 id
- controller: bool 指定某一个元素是否可以控制行的展开
- hasChild: bool 指定某一个 tr 元素是否有孩子（动态加载需用到）
- isFirstOne: bool 指定某一个 tr 元素是否是第一个孩子（自动生成属性，只读）
- isLastOne: bool 指定某一个 tr 元素是否是最后一个孩子（自动生成属性，只读）
- prevId: string 前一个兄弟节点的 id（自动生成属性，只读）
- depth: string 当前行的深度（自动生成属性，只读）

## [#](https://funtl.com/zh/supplement1/jQuery-TreeTable.html#使用方法)使用方法

```js
$(function () {
    $("#treeTable").treeTable({
        expandLevel: 2,
        column: 1
    });
});
```

### [#](https://funtl.com/zh/supplement1/jQuery-TreeTable.html#html-结构代码)HTML 结构代码

```html
<table id="treeTable1" style="width:100%">
    <tr>
        <td style="width:200px;">标题</td>
        <td>内容</td>
    </tr>
    <tr id="1">
        <td><span controller="true">1</span></td>
        <td>内容</td></tr>
    <tr id="2" pId="1">
        <td><span controller="true">2</span></td>
        <td>内容</td></tr>
    <tr id="3" pId="2">
        <td>3</td>
        <td>内容</td>
    </tr>
    <tr id="4" pId="2">
        <td>4</td>
        <td>内容</td>
    </tr>
    <tr id="5" pId="4">
        <td>4.1</td>
        <td>内容</td>
    </tr>
    <tr id="6" pId="1" hasChild="true">
        <td>5</td>
        <td>注意这个节点是动态加载的</td>
    </tr>
    <tr id="7">
        <td>8</td>
        <td>内容</td>
    </tr>
</table>
```

### [#](https://funtl.com/zh/supplement1/jQuery-TreeTable.html#注意事项)注意事项

这里的 HTML 结构是经过排序的，每行数据必须紧跟其子类目的数据项，结构类似于：

```text
类目 1
    类目 1-1
    类目 1-2
    ...
类目 2
    类目 2-1
类目 3
类目 4
```

服务端排序代码如下（以下方式是根据之前提供的 `myshop` 数据库中的 `tb_content_category` 表结构得来，有需要请加群获取）：

```java
// 调用方法，0 为约定好的根节点
sortList(sourceList, targetList, 0L);

/**
 * 排序
 * @param sourceList 数据源集合
 * @param targetList 排序后的集合
 * @param parentId 当前的父级类目 ID
 */
private void sortList(List<TbContentCategory> sourceList, List<TbContentCategory> targetList, Long parentId) {
    for (TbContentCategory sourceContentCategory : sourceList) {
        if (sourceContentCategory.getParentId().equals(parentId)) {
            targetList.add(sourceContentCategory);

            // 判断有没有子节点，有则继续追加
            if (sourceContentCategory.getParent()) {
                for (TbContentCategory tbContentCategory : sourceList) {
                    if (tbContentCategory.getParentId().equals(sourceContentCategory.getId())) {
                        sortList(sourceList, targetList, sourceContentCategory.getId());
                        break;
                    }
                }
            }
        }
    }
}
```

### [#](https://funtl.com/zh/supplement1/jQuery-TreeTable.html#演示效果)演示效果

![img](https://funtl.com/assets/Lusifer1529862549.png)

上次更新: 2019-4-19 19:23:15