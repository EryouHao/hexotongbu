---
title: css练习之-左侧固定右侧自适应布局
date: 2016-07-23 09:42:51
tags: css 布局
categories: 于前端
---

之前有看到面试题有这样一个题：实现左侧固定右侧自适应布局，当时一下子没有想出来，现在整理一下。想了三种方式实现
 >方式一 左侧浮动，右侧设置margin-left宽度为左侧宽度
 	方式二 左右均浮动，左侧设置负margin，右侧设置margin-left(有bug)
 	方式三 通过设置overflow:hidden;来触发right-3生成BFC

<!-- more -->

## 方式一
方式一 左侧浮动，右侧设置`margin-left`宽度为左侧宽度
``` css
.left-1{
	float: left;
	width: 100px;
	height: 100px;
	background: #eee;
}
.right-1{
	background: #ccc;
	min-height: 100px;
	margin-left: 100px;
}
```
## 方式二
左右均浮动，左侧设置负`margin`，右侧设置`margin-left`。会有隐藏文字的bug不采用
``` css
.test-2{
	overflow: hidden;
}
.left-2{
	float: left;
	width: 100px;
	height: 100px;
	background: #eee;
	margin-right: -100%;
}
.right-2{
	float: left;
	width: 100%;
	min-height: 100px;
	margin-left: 100px;
	background: #ccc;
}
```
## 方式三
通过设置`overflow:hidden;`来触发right-3生成BFC.清除浮动时给父元素设置`overflow:hidden;`也是利用此原理
``` css
.left-3{
	width: 100px;
	height: 100px;
	background: #eee;
	float: left;
}
.right-3{
	min-height: 100px;
	overflow: hidden;
	background: #ccc;
}
```
## 写在后面
起初不是很明白BFC的原理，多谢[前端里](http://www.yyyweb.com/)的小鱼大神！附上链接：
[前端精选文摘：BFC 神奇背后的原理](http://www.yyyweb.com/830.html)
BFC也确实是个好东西：
[火狐技术文档](https://developer.mozilla.org/zh-CN/docs/Web/Guide/CSS/Block_formatting_context)
实现demo：
http://fehey.com/demos/html/css练习之-左侧固定右侧自适应布局.html