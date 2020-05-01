# jQuery zTree

zTree 是一个依靠 jQuery 实现的多功能 “树插件”。优异的性能、灵活的配置、多种功能的组合是 zTree 最大优点。

本教程是为了配合`项目实战` 的简易教程，主要实现了 `异步加载` 树形结构数据的功能，具体用法请参考官方文档。

## [#](https://funtl.com/zh/supplement1/jQuery-zTree.html#页面引用)页面引用

CSS 部分

```html
<link rel="stylesheet" href="/static/assets/plugins/jquery-ztree/css/zTreeStyle/zTreeStyle.min.css" />
```

JS 部分

```html
<script src="/static/assets/plugins/jquery-ztree/js/jquery.ztree.core-3.5.min.js"></script>
```

## [#](https://funtl.com/zh/supplement1/jQuery-zTree.html#使用方法)使用方法

开启 zTree 异步加载数据的功能，案例代码如下：

```js
var setting = {
    view: {
        // 禁止多选
        selectedMulti: false
    },
    async: {
        // 开启异步加载功能
        enable: true,
        // 远程访问地址
        url: "/content/category/tree/data",
        // 选择父节点时会自动将节点中的参数传回服务器再重新取结果
        autoParam: ["id"]
    }
};

// 初始化 zTree 控件
$.fn.zTree.init($("#myTree"), setting);
// 绑定事件
$("#btnModalOk").bind("click", function () {
    // 获取 zTree 控件
    var zTree = $.fn.zTree.getZTreeObj("myTree");
    // 获取已选中的节点
    var nodes = zTree.getSelectedNodes();
    if (nodes.length == 0) {
        alert("请先选择一个父节点");
    }

    else {
        var node = nodes[0];
        alert(node.id);
    }
});
```

### [#](https://funtl.com/zh/supplement1/jQuery-zTree.html#html-结构代码)HTML 结构代码

```html
<ul id="myTree" class="ztree"></ul>
```

### [#](https://funtl.com/zh/supplement1/jQuery-zTree.html#服务器关键代码)服务器关键代码

此为 `Controller` 关键代码，其余部分可参考 [【视频教程】](https://www.bilibili.com/video/av25584906) 完成

```java
@ResponseBody
@RequestMapping(value = "tree/data", method = RequestMethod.POST)
public List<TbContentCategory> treeData(String id) {
    if (id == null) {
        id = "0";
    }
    List<TbContentCategory> tbContentCategories = tbContentCategoryService.selectByPid(Long.parseLong(id));
    return tbContentCategories;
}
```

### [#](https://funtl.com/zh/supplement1/jQuery-zTree.html#演示效果)演示效果

![img](https://funtl.com/assets/Lusifer1529873938.png)

上次更新: 2019-4-19 19:23:15