---
title: IE6/7下浮动元素margin值失效的问题
date: 2016-10-05 15:22:47
tags: 兼容
categories: 于前端
---
IE6/7下浮动元素会产生margin值失效的问题，解决方法可以采用设置外层容器，然后设置`padding`值
<!-- more -->
解决前：
``` html
    <style>
        .clear {
            zoom:1;
        }
        .clear:after {
            content: '';
            display: block;
            clear: both;
        }
        #div1 {
            width: 100px;
            height: 100px;
            background: #ccc;
            float: left;
            margin-bottom: 10px;
        }
        #div2 {
            width: 200px;
            height: 100px;
            background: #eee;
        }
    </style>
    <div class="clear">
        <div id="div1"></div>
    </div>
    <div id="div2"></div>
```
解决后(去掉div1的`margin-bottom`)：
``` html
    .wrap {
        padding-bottom: 10px;
    }
    <div class="wrap clear">
        <div id="div1"></div>
    </div>
    <div id="div2"></div>
```
END.