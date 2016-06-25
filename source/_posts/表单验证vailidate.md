---
title: 超强大表单验证插件jquery.validate.js的使用心得（初级）
date: 2016-06-25 10:27:06
tags: 表单验证
categories: 前端说
---

### 前言
先上插件官网传送门：
http://jqueryvalidation.org/
> 最近在做项目的时候，在处理表单验证、非空判断、邮箱格式等等时，由于工期比较紧，于是选择了使用了jquery.validate.min.js这款非常棒的插件。总结下用法,方便查看.

<!-- more -->
### 插件引入
``` html
    <!-- Jquery Validate -->
    <script src="js/plugins/validate/jquery.validate.min.js"></script>

    <script src="js/plugins/validate/messages_zh.min.js"></script>
```
1.对插件js文件的引用
2.对插件中文包的引用，如果不引用默认是英文的提示文字比如：
: required: "This field is required."
### 插件使用


``` javascript
        <script>
        $(document).ready(function () {

            $("#form").validate({
                rules: {
                    password: {
                        required: true,
                        minlength: 3
                    },
                    url: {
                        required: true,
                        url: true
                    },
                    number: {
                        required: true,
                        number: true
                    },
                    min: {
                        required: true,
                        minlength: 6
                    },
                    "max.test": {
                        required: true,
                        maxlength: 4
                    },
                    dateISO: {
                        required: true,
                        dateISO:true 
                    }
                },
                messages:{
                    dateISO:"请选择日期",
                    "max.test":"您输入过长",
                }
            });
        });
    </script>
```
说明：
对需要设置的form来命名相同的id，上面例子为
``` html
    <form id="form" novalidate="novalidate" role="form">
```

对input标签加入对应着rules里的name属性：

``` html
    <input type="text" class="form-control" name="min" placeholder="请输入数量">
```

如果是只设置必填项的话也可以在class里加一个`required`。
对select标签因为不涉及格式错误的情况，所以只添加必填就可以了

``` html
    <select class="form-control required">
        <option value="">请选择</option>
        <option value="1">选项 1</option>
        <option value="2">选项 2</option>
        <option value="3">选项 3</option>
     </select>
```
其他rules和messages说明：
> 可以参照菜鸟教程里的表格：
传送门：http://www.runoob.com/jquery/jquery-plugin-validate.html

当然了也可以自定义扩展验证
``` javascript
    jQuery.validator.addMethod("isZipCode", function(value, element) {   
            var tel = /^[0-9]{6}$/;
            return this.optional(element) || (tel.test(value));
        }, "请正确填写您的邮政编码");
```
这个最好写在一个js文件中，然后引用放在上面两个js文件之后，然后在rules里添加就可以了。

jquery.validate.js提供了一套默认的样式，自己也可以自定义样式和展现位置。

### 开发时遇到问题（Ending）
开发时由于input里面的name属性要用类似   `Abc.test `格式的进行传值。所以在rules规则里定义的时候需要写成上面的`"max.test"`形式，一定要有双引号，不然函数会报错。因为点“.”在javascript中是特殊字符。
