# wangEditor

轻量级 web 富文本编辑器，配置方便，使用简单。支持 IE10+ 浏览器。

- 官网：http://www.wangEditor.com
- 文档：http://www.kancloud.cn/wangfupeng/wangeditor3/332599
- 源码：http://github.com/wangfupeng1988/wangEditor

## [#](https://funtl.com/zh/supplement1/wangEditor-富文本编辑器.html#什么是富文本编辑器)什么是富文本编辑器

富文本编辑器，Rich Text Editor, 简称 RTE, 它提供类似于 Microsoft Word 的编辑功能，容易被不会编写 HTML 的用户并需要设置各种文本格式的用户所喜爱。它的应用也越来越广泛。最先只有 IE 浏览器支持，其它浏览器相继跟进，在功能的丰富性来说，还是 IE 强些。虽然没有一个统一的标准，但对于最基本的功能，各浏览器提供的 API 基本一致，从而使编写一个跨浏览器的富文本编辑器成为可能。

## [#](https://funtl.com/zh/supplement1/wangEditor-富文本编辑器.html#页面引入)页面引入

CSS 部分

```html
<link rel="stylesheet" href="/static/assets/plugins/wangEditor/wangEditor.min.css" />
```

JS 部分

```html
<script src="/static/assets/plugins/wangEditor/wangEditor.min.js"></script>
```

## [#](https://funtl.com/zh/supplement1/wangEditor-富文本编辑器.html#启用-wangeditor)启用 wangEditor

只需要一个 `div` 元素，用 JavaScript 代码启用即可

HTML 结构如下：

```html
<div id="editor"></div>
```

JavaScript 启用代码如下：

```js
var E = window.wangEditor;
var editor = new E('#editor');
editor.create();
```

## [#](https://funtl.com/zh/supplement1/wangEditor-富文本编辑器.html#效果演示)效果演示

配置完成后，在浏览器端显示如下：

![img](https://funtl.com/assets/Lusifer1530124826.png)

### [#](https://funtl.com/zh/supplement1/wangEditor-富文本编辑器.html#服务端支持)服务端支持

配置方式同 [Dropzone 图片上传插件](https://funtl.com/2018/06/27/appose/Dropzone-图片上传插件/#服务端支持)

### [#](https://funtl.com/zh/supplement1/wangEditor-富文本编辑器.html#控制器关键代码参考)控制器关键代码参考

```java
package com.funtl.my.shop.web.admin.web.controller;

import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestMethod;
import org.springframework.web.bind.annotation.ResponseBody;
import org.springframework.web.multipart.MultipartFile;

import javax.servlet.http.HttpServletRequest;
import java.io.File;
import java.io.IOException;
import java.util.HashMap;
import java.util.Map;
import java.util.UUID;

/**
 * 文件上传控制器
 * <p>Title: UploadController</p>
 * <p>Description: </p>
 *
 * @author Lusifer
 * @version 1.0.0
 * @date 2018/6/27 14:32
 */
@Controller
public class UploadController {

    private static final String UPLOAD_PATH = "/static/upload/";

    /**
     * 文件上传
     *
     * @return
     */
    @ResponseBody
    @RequestMapping(value = "upload", method = RequestMethod.POST)
    public Map<String, Object> upload(MultipartFile editorFile, HttpServletRequest request) {
        Map<String, Object> result = new HashMap<>();

        // 获取文件后缀
        String fileName = editorFile.getOriginalFilename();
        String fileSuffix = fileName.substring(fileName.lastIndexOf("."));

        // 文件存放路径
        String filePath = request.getSession().getServletContext().getRealPath(UPLOAD_PATH);

        // 判断路径是否存在，不存在则创建文件夹
        File file = new File(filePath);
        if (!file.exists()) {
            file.mkdir();
        }

        // 将文件写入目标
        file = new File(filePath, UUID.randomUUID() + fileSuffix);
        try {
            editorFile.transferTo(file);
        } catch (IOException e) {
            e.printStackTrace();
        }

        // 获取服务端路径
        String serverPath = String.format("%s://%s:%s%s%s", request.getScheme(), request.getServerName(), request.getServerPort(), request.getContextPath(), UPLOAD_PATH);
        
        // 返回给 wangEditor 的数据格式
        result.put("errno", 0);
        result.put("data", new String[]{serverPath + file.getName()});
        return result;
    }
}
```

相比 `Dropzone 图片上传插件` 一节，控制器代码的主要差别在于接口返回的数据格式，官方要求的格式如下：

```json
{
    // errno 即错误代码，0 表示没有错误。
    //       如果有错误，errno != 0，可通过下文中的监听函数 fail 拿到该错误码进行自定义处理
    "errno": 0,

    // data 是一个数组，返回若干图片的线上地址
    "data": [
        "图片1地址",
        "图片2地址",
        "……"
    ]
}
```

上次更新: 2019-4-19 19:23:15