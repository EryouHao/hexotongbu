---
title: vue初探-日期选择器
date: 2016-12-05 15:11:20
tags: vue
categories: JavaScript
---
之前用jquery用面向对象的方法实现过日期选择器，最近在练习vue，现在用vue实现一遍。发现vue用数据驱动的方式来实现，感觉还不错。
## 需求设定
> 1.实现一个日期选择器，默认显示，today高亮显示。
> 2.能够点击上一月、下一月进行日期跳转。
> 3.点击某一天能够拿到日期

## 思路分析

之前用jquery实现的时候可能会考虑将dom封装到js中，在js中拼接html字符串，在页面调用的地方直接引用。考虑到vue可以进行组件式开发，日期组件完全可以单独提取出一个vue文件，所以这里直接将dom写在html中，开发时可写在template里。

实现日期选择有两个比较关键的方法：1.获取当月天数，以便循环遍历多少天。2.获取当月第一天是星期几，以便确定第一天显示在哪里。

在编写组件时，我们选择维护三个变量`nowYear`,`nowMonth`,`nowDate`,即vue实例上挂在的当前年、月、日。

在进行数据初始化，也可以看做组件初始化显示时，首先为这三个变量附初值
``` javascript
var date = new Date();
this.nowYear = date.getFullYear();
this.nowMonth = date.getMonth();
this.nowDate = date.getDate();
```
然后，将传统方法中的init函数，也就是原来的dom拼接函数，在这里仅仅操作一个数组，即维护一个数组days,存储需要显示的当月日期数据。

同样我们利用vue体统的@click可以很方便的绑定事件，然后进行days的更新。当days更新时，vue的watcher就会发现并告诉dom更新，利用Vm.$nextTick方法，执行回调函数。即
``` javascript
this.$nextTick(() => {
  this.nowDate();
  this.init();
})
```
## 效果演示
目前只实现到需求设定的程度，于实际应用可能稍有差距，可以理解下思路，使用时自行实现

demo:(http://fehey.com/Vue-items/vue-calendar/)
源码：(https://github.com/EryouHao/Vue-items/blob/gh-pages/vue-calendar/index.html)
