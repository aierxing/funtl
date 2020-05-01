# [#](https://funtl.com/zh/supplement1/jQuery-Datatables.html#jquery-datatables)jQuery Datatables

DataTables 是一款 jQuery 表格插件。它是一个高度灵活的工具，可以将任何HTML表格添加高级的交互功能。

- 中文网站：http://www.datatables.club/
- 实例索引：http://www.datatables.club/example/
- 参考手册：http://www.datatables.club/manual/
- 帮助文档：http://www.datatables.club/reference/

## [#](https://funtl.com/zh/supplement1/jQuery-Datatables.html#页面引用)页面引用

CSS 部分

```text
<!-- DataTables -->
<link rel="stylesheet" href="/static/assets/bower_components/datatables.net-bs/css/dataTables.bootstrap.min.css">
```

1
2

JS 部分

```text
<!-- DataTables -->
<script src="/static/assets/bower_components/datatables.net/js/jquery.dataTables.min.js"></script>
<script src="/static/assets/bower_components/datatables.net-bs/js/dataTables.bootstrap.min.js"></script>
```

1
2
3

使用，启用 0 配置模式

```text
$('#dataTable').DataTable();
```

1

## [#](https://funtl.com/zh/supplement1/jQuery-Datatables.html#分页查询案例)分页查询案例

### [#](https://funtl.com/zh/supplement1/jQuery-Datatables.html#服务端)服务端

#### [#](https://funtl.com/zh/supplement1/jQuery-Datatables.html#mybatis-映射文件关键代码)MyBatis 映射文件关键代码

DataTables 分页需要提供查询数据的总笔数，以下为查询总笔数的关键代码：

```xml
<select id="count" resultType="java.lang.Integer">
  SELECT COUNT(*) FROM tb_user
</select>
```

这里我们使用 MySQL 作为数据库，由于需要传入分页参数，这里我们还使用了 `Map` 作为查询参数类型，以下为 MySQL 分页查询的关键代码：

```xml
<select id="page" resultType="TbUser" parameterType="java.util.Map">
    SELECT
    <include refid="tbUserColumns" />
    FROM
    tb_user AS a LIMIT #{page}, #{pageSize}
</select>
```

#### [#](https://funtl.com/zh/supplement1/jQuery-Datatables.html#定义数据访问接口)定义数据访问接口

```java
/**
 * 分页查询
 * @param params
 * @return
 */
List<TbUser> page(Map<String, Object> params);

/**
 * 查询笔数
 * @return
 */
int count();
```

#### [#](https://funtl.com/zh/supplement1/jQuery-Datatables.html#定义通用的分页展示对象)定义通用的分页展示对象

创建一个名为 `PageInfo` 的分页数据展示对象，代码如下：

```java
package com.funtl.my.shop.commons.dto;

import com.funtl.my.shop.commons.persistence.BaseEntity;

import java.io.Serializable;
import java.util.List;

/**
 * 分页展示对象
 * <p>Title: PageInfo</p>
 * <p>Description: </p>
 *
 * @author Lusifer
 * @version 1.0.0
 * @date 2018/6/21 5:17
 */
public class PageInfo<T extends BaseEntity> implements Serializable {
    private int draw;
    private int recordsTotal;
    private int recordsFiltered;
    private List<T> data;
    private String error;

    public int getDraw() {
        return draw;
    }

    public void setDraw(int draw) {
        this.draw = draw;
    }

    public int getRecordsTotal() {
        return recordsTotal;
    }

    public void setRecordsTotal(int recordsTotal) {
        this.recordsTotal = recordsTotal;
    }

    public int getRecordsFiltered() {
        return recordsFiltered;
    }

    public void setRecordsFiltered(int recordsFiltered) {
        this.recordsFiltered = recordsFiltered;
    }

    public List<T> getData() {
        return data;
    }

    public void setData(List<T> data) {
        this.data = data;
    }

    public String getError() {
        return error;
    }

    public void setError(String error) {
        this.error = error;
    }
}
```

#### [#](https://funtl.com/zh/supplement1/jQuery-Datatables.html#业务逻辑层关键代码)业务逻辑层关键代码

```java
@Override
public PageInfo<TbUser> page(Map<String, Object> params) {
    PageInfo<TbUser> pageInfo = new PageInfo<>();

    int count = tbUserDao.count();
    List<TbUser> tbUsers = tbUserDao.page(params);

    pageInfo.setRecordsTotal(count);
    pageInfo.setRecordsFiltered(count);
    pageInfo.setData(tbUsers);

    return pageInfo;
}
```

#### [#](https://funtl.com/zh/supplement1/jQuery-Datatables.html#控制器层关键代码)控制器层关键代码

```java
/**
 * 分页查询
 *
 * @param request
 * @return
 */
@ResponseBody
@RequestMapping(value = "page", method = RequestMethod.GET)
public PageInfo<TbUser> page(HttpServletRequest request) {
    String draw = request.getParameter("draw");
    int start = Integer.parseInt(request.getParameter("start"));
    int length = Integer.parseInt(request.getParameter("length"));

    Map<String, Object> params = new HashMap<>();
    params.put("page", start);
    params.put("pageSize", length);

    PageInfo<TbUser> pageInfo = tbUserService.page(params);
    pageInfo.setDraw(draw == null ? 0 : Integer.parseInt(draw));
    return pageInfo;
}
```

### [#](https://funtl.com/zh/supplement1/jQuery-Datatables.html#客户端)客户端

使用 DataTables 分页功能，需要开启一些列的相关配置，分页的数据结果是由 Ajax 请求获取并解析 JSON 格式数据自动封装进表格的，代码如下：

```js
$("#dataTable").DataTable({
    // 是否开启本地分页
    "paging": true,
    // 是否开启本地排序
    "ordering": false,
    // 是否显示左下角信息
    "info": true,
    // 是否允许用户改变表格每页显示的记录数
    "lengthChange": false,
    // 是否显示处理状态(排序的时候，数据很多耗费时间长的话，也会显示这个)
    "processing": true,
    // 是否允许 DataTables 开启本地搜索
    "searching": false,
    // 是否开启服务器模式
    "serverSide": true,
    // 控制 DataTables 的延迟渲染，可以提高初始化的速度
    "deferRender": true,
    // 增加或修改通过 Ajax 提交到服务端的请求数据
    "ajax": {
        "url": "/user/page"
    },
    // 分页按钮显示选项
    "pagingType": "full_numbers",
    // 设置列的数据源
    "columns": [
        {
            "data": function (row, type, val, meta) {
                return '<input id="' + row.id + '" type="checkbox" class="minimal" />';
            }
        },
        {"data": "id"},
        {"data": "username"},
        {"data": "phone"},
        {"data": "email"},
        {"data": "updated"},
        {
            "data": function (row, type, val, meta) {
                return '<a href="#" type="button" class="btn btn-sm btn-default"><i class="fa fa-search"></i> 查看</a>&nbsp;&nbsp;&nbsp;' +
                    '<a href="#" type="button" class="btn btn-sm btn-primary"><i class="fa fa-edit"></i> 编辑</a>&nbsp;&nbsp;&nbsp;' +
                    '<a href="#" type="button" class="btn btn-sm btn-danger"><i class="fa fa-trash-o"></i> 删除</a>'
            }
        }
    ],
    // 表格重绘的回调函数
    "drawCallback": function (settings) {
        App.initCheckbox();
    },
    // 国际化
    "language": {
        "sProcessing": "处理中...",
        "sLengthMenu": "显示 _MENU_ 项结果",
        "sZeroRecords": "没有匹配结果",
        "sInfo": "显示第 _START_ 至 _END_ 项结果，共 _TOTAL_ 项",
        "sInfoEmpty": "显示第 0 至 0 项结果，共 0 项",
        "sInfoFiltered": "(由 _MAX_ 项结果过滤)",
        "sInfoPostFix": "",
        "sSearch": "搜索:",
        "sUrl": "",
        "sEmptyTable": "表中数据为空",
        "sLoadingRecords": "载入中...",
        "sInfoThousands": ",",
        "oPaginate": {
            "sFirst": "首页",
            "sPrevious": "上页",
            "sNext": "下页",
            "sLast": "末页"
        },
        "oAria": {
            "sSortAscending": ": 以升序排列此列",
            "sSortDescending": ": 以降序排列此列"
        }
    }
})
```

### [#](https://funtl.com/zh/supplement1/jQuery-Datatables.html#参考以下文档完成)参考以下文档完成

- 配置选项：http://www.datatables.club/reference/option/
- 服务器处理：http://www.datatables.club/manual/server-side.html
- 设置列的数据源：https://datatables.net/reference/option/columns.data
- 国际化：http://www.datatables.club/manual/i18n.html

上次更新: 2018-12-29 5:44:48