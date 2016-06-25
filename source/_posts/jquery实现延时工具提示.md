---
title: jquery实现延时工具提示
date: 2016-06-25 11:04:02
tags: jquery
categories: 于前端
---
最近项目中有用到工具提示的功能，故整理下来。

### 效果截图
![tooltip](/postimg/tooltip.png)
<!-- more -->
### 实现功能
>1.美化后的复选框
2.鼠标移动到显示房间号的复选框时显示title内容
3.实现延时显示提示框

### 思路整理
复选框用input加span实现，关键属性 `opacity: 0`; 
工具提示用jquery中的`hover()`方法触发，`attr()`方法来获取title属性内容，`append()`方法来添加工具提示的div，`delay()`方法来进行`show()`方法和`remove()`方法的延时执行。
### 主要代码

#### html部分
``` html
 <li> 
 <input type="checkbox" checked="checked" title="房间号：105，一单元，c1座一单元，三室两厅" /> 
 <span>101</span> 
</li> 
<li> 
 <input type="checkbox" checked="checked" title="房间号：105，一单元，c1座一单元，三室两厅" /> 
 <span>102</span> 
</li> ﻿
```
#### CSS部分
``` css

.room-con ul li input{
    display: block;
    width: 34px;
    height: 34px;
    margin:0;
    padding:0;
    opacity: 0;
    z-index: 100;
    position: absolute;
    top: 0;
    left: 0;
    cursor: pointer;
}
.room-con ul li span{
    position: absolute;
    top: 0;
    left: 0;
    color: #fff;
    padding:10px 5px;
    margin-left: 0;
}
.room-con ul li input[type="checkbox"] + span {
  background: #F08080;
}

.room-con ul li input[type="checkbox"]:checked + span {
  background: #40E0D0;

```
#### jquery部分 
delay方法是1.4.3版本之后支持的，所以如果delay方法不起作用的话看下页面用的jquery版本
``` javascript
//房间号上的工具提示
    $(".room-con ul li input").hover(function(){ 
        var curtitle = $(this).attr('title');
        $(this).parent().append("<div class="+"tips"+">"+ curtitle +"</div>");
        $(".tips").delay(500).show(0); 
    },function(){
        $(".tips").delay(500).remove(0);
    });
```
