---
title: vue初探-简易留言板
date: 2016-11-05 12:12:34
tags: vue
categories: JavaScript
---

学完vue的基础语法之后，练手一下，从最基本的留言板开刀吧。功能不多，主要为了熟悉vue的基础语法使用。详细vue教程请移步(https://vuefe.cn/)
<!-- more -->

## 技术框架
> 1.vue.js 2.0
>
> 2.bootstrap

## 语法概述

这里只写一点此例子用到的一些语法知识，详细API请移步：(https://vuefe.cn/api/)

> @click: 绑定点击事件 @ 是v-on:的缩写
>
> v-model: 在表单控件或者组件上创建双向绑定
>
> v-show: 根据表达式之真假值，确实是否显示
>
> v-for：循环显示列表
>
> :class: 绑定class, : 是v-bind:的缩写

``` javascript
// vue 基础模板
var app = new Vue({
    el: '#app', // 绑定的DOM，vue在此DOM内生效
    data: { // 绑定的数据
        msg: 'message'
    },
    watch: { // 监听变化
        msgs: {
            // TODO...
        }
    },
    methods: { // 事件方法
        addMsg: function () {

        },
        ...
    }
})
```
## 实现思路

    1.利用localStorage实现本地存储
这里借鉴了官网的todoMVC例子中思路，封装一个msgStorage对象，进行读取和存储数据
用的时候转换成JSON对象，存的时候是JSON格式的字符串。

    2.增加留言、清空留言功能
这里对留言列表进行了监听，监听Vue实例中的msgs变化后，更新localStorage中msgs，同时更新列表，当执行增加和删除时更新Vue实例中的msgs

## 写在后面

可能叙述中有错误的地方，欢迎指正，交流~，最后附上demo地址(http://fehey.com/Vue-items/msgboard/) ,源码：(https://github.com/EryouHao/Vue-items)
