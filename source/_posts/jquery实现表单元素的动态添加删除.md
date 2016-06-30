---
title: jquery实现表单元素的动态添加删除
date: 2016-06-30 19:28:56
tags: jquery
categories: 于前端
---
利用jquery实现表单元素的动态添加删除，一开始在网上找了一个插件，不过觉得不是特别适合所以决定自己写一个方法。由于时间和经验的关系并没有封装成插件，待后来不太忙时，封装成插件
<!-- more -->
### 实现功能
 1. 一个表单元素时，只显示增加按钮
 2. 两个及多个表单元素时，最后一个显示增加按钮，之前的只显示删除按钮
 3. 当删除到只剩下一个时，恢复成只显示一个增加按钮

### 效果截图
![addform](/postimg/addform.png)
### 核心方法
jquery的clone( )方法【克隆匹配的DOM元素并且选中这些克隆的副本】
具体可以查看API
### 主要代码
html部分
``` html
<p class="dt-p">
  <label>维修厂商</label>
  <input class="text-input" type="text"/>
  <label style="float: none;">金额</label>
  <input class="text-input wd100" type="text"/>
  <a href="javascript:(0);" class="delete">删除</a>
  <a href="javascript:(0);" class="add">增加</a>
</p>﻿
```
css部分
``` javascript
<script>
        $(function() {
            //根据页面指定p标签的个数显示增加、删除按钮
            function checkp(){
                var plen = $('.dt-p').length;
                if (plen === 1) {
                    $('.delete').hide();
                    $('.add').show();
                } else {
                    $('.delete').show();
                    $('.dt-p').last().children(":last").show();
                }
            };
            checkp();
            //增加
            $('.add').click(function() {
                //核心方法：clone()，其克隆后的副本依然可以继续克隆
                //此处用的this parent 如果直接用class选择器的话会一次clone多个指定p标签
                $(($(this).parent())).clone(true).insertAfter(($(this).parent()));
                $(this).hide();
                $(this).prev().show();
                checkp();
            });
            //删除
            $('.delete').click(function() {
                $(($(this).parent())).remove().slideUp();
                checkp();
            });
        });
    </script>﻿
```
