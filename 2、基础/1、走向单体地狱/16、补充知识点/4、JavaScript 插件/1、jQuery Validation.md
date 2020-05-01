# [#](https://funtl.com/zh/supplement1/jQuery-Validation.html#jquery-validation)jQuery Validation

jQuery Validation 前端表单验证框架

## [#](https://funtl.com/zh/supplement1/jQuery-Validation.html#页面引用)页面引用

```html
<!-- jQuery Validation 1.14.0 -->
<script src="/static/assets/plugins/jquery-validation/js/jquery.validate.js"></script>
<script src="/static/assets/plugins/jquery-validation/js/additional-methods.js"></script>
<script src="/static/assets/plugins/jquery-validation/js/localization/messages_zh.js"></script>
```

## [#](https://funtl.com/zh/supplement1/jQuery-Validation.html#使用案例)使用案例

```js
<form:input path="username" class="form-control required" placeholder="用户名" />

$(function () {
    $("#inputForm").validate({
        errorElement: 'span',
        errorClass: 'help-block',

        errorPlacement: function (error, element) {
            element.parent().parent().attr("class", "form-group has-error");
            error.insertAfter(element);
        }
    });
});
```

## [#](https://funtl.com/zh/supplement1/jQuery-Validation.html#默认校验规则说明)默认校验规则说明

- `required`：true 必输字段
- `remote`：`check.php` 使用 ajax 方法调用 check.php 验证输入值
- `email`：true 必须输入正确格式的电子邮件
- `url`：true 必须输入正确格式的网址
- `date`：true 必须输入正确格式的日期
- `dateISO`：true 必须输入正确格式的日期(ISO)，例如：2009-06-23，1998/01/22 只验证格式，不验证有效性
- `number`：true 必须输入合法的数字(负数，小数)
- `digits`：true 必须输入整数
- `creditcard`： 必须输入合法的信用卡号
- `equalTo`：`#field`，输入值必须和 #field 相同
- `accept`： 输入拥有合法后缀名的字符串（上传文件的后缀）
- `maxlength`：5，输入长度最多是5的字符串(汉字算一个字符)
- `minlength`：10，输入长度最小是10的字符串(汉字算一个字符)
- `rangelength`：`[5,10]`，输入长度必须介于 5 和 10 之间的字符串")(汉字算一个字符)
- `range`：`[5,10]`，输入值必须介于 5 和 10 之间
- `max`：5，输入值不能大于 5
- `min`：10，输入值不能小于 10

## [#](https://funtl.com/zh/supplement1/jQuery-Validation.html#自定义校验规则)自定义校验规则

```js
$.validator.addMethod("mobile", function(value, element) {
    var length = value.length;
    var mobile =  /^(((13[0-9]{1})|(15[0-9]{1}))+\d{8})$/;
    return this.optional(element) || (length == 11 && mobile.test(value));
}, "手机号码格式错误");
```

## [#](https://funtl.com/zh/supplement1/jQuery-Validation.html#封装校验器)封装校验器

```javascript
/**
 * jQuery 有效性验证
 * @constructor
 */
var Validate = function () {

    /**
     * 初始化校验规则
     */
    var handlerInit = function () {
        $.validator.addMethod("mobile", function (value, element) {
            var length = value.length;
            var mobile = /^(((13[0-9]{1})|(15[0-9]{1}))+\d{8})$/;
            return this.optional(element) || (length == 11 && mobile.test(value));
        }, "手机号码格式错误");
    };

    /**
     * 表单验证
     * @param formId
     */
    var handlerValidate = function (formId) {
        $("#" + formId).validate({
            errorElement: 'span',
            errorClass: 'help-block',
            errorPlacement: function (error, element) {
                element.parent().parent().attr("class", "form-group has-error");
                error.insertAfter(element);
            }
        });
    };

    return {
        /**
         * 初始化校验规则
         */
        init: function () {
            handlerInit();
        },

        /**
         * 表单验证
         * @param formId
         */
        validateForm: function (formId) {
            handlerValidate(formId);
        }
    }
}();

$(function () {
   Validate.init();
});
```

上次更新: 2018-12-29 5:44:48