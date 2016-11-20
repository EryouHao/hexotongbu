---
title: Sticky footers实现套路
date: 2016-11-20 20:33:47
tags: css 移动端
---

无论是pc还是移动端，有一种布局叫做Sticky footers，何为Sticky footers？百度翻译：“粘粘的页脚”[笑哭],
不过意思也算对吧，就是如果页面内容不够长的时候，页脚粘贴在视窗底部；如果内容足够长时，页脚块会附在内容区块下面。那么如何来实现这种效果呢，今天来用两种方式聊一聊。
<!-- more -->

## 实现原理
>1.利用包裹层，用高度100%来实现
>2.利用flex + 高度100vh来实现

## 第一种方式

设置body的高度为100%，然后设置设置内容容器的最小高度为100%，然后设置负的margin-bottom，负值为页脚高度，然后利用伪类，利用容器的after占位。
``` html
html,body {
	height: 100%;
}
.page-wrap{
	min-height: 100%;
	margin-bottom: -142px;
}
.page-wrap:after {
	content: '';
	display: block;
}
footer,.page-wrap:after {
	height: 142px;
}
----------------------------------
<body>
	<div class="page-wrap"></div>
	<footer></footer>
</body>
```
## 第二种方式
首先提一个概念：`vh` ：相对于视窗的高度：视窗高度是100vh。
将body设置为 `display:flex;` 这时你会发现页面的布局变成了横向布局，因为flex布局默认主轴方向为 `row` ，即横向。然后利用 `flex-flow: column;` 将主轴方向设置为垂直方向。关于flex的语法，推荐阮一峰老师的blog[Flex 布局教程：语法篇](http://www.ruanyifeng.com/blog/2015/07/flex-grammar.html?utm_source=tuicool),
然后设置内容容器`flex: 1`。

这里稍微介绍一下`flex: 1`
`flex: 1` 等同于 `flex: 1 1 0%`,即等价于`flex-grow: 1; flex-shrink: 1;flex-basis: 0%;`前两个根据阮老师的教程很好理解，那么第三个`flex-basis: 0%`是什么意思呢？
`flex-basis`有三种取值情况：
>1.auto: 先检索该元素的尺寸，如果不为auto，则取固定值；如果也是auto，则取值content
>2.content: 指该元素的内容自动布局。
>3.百分比: 根据其父容器的尺寸计算。如果父容器的尺寸未定义，则取值和设为auto相同。

所以，设置为 `flex: 1` 之后，内容容器内容高度小于父容器时，则取父容器高度，如果大于父容器高度时则取内容高度。

理解了上面这些之后，下面是实现的一个简单例子。
``` html
body{
	display: flex;
	flex-flow: column;
	min-height: 100vh;
}
main {
	flex: 1;
}
-----------
<body>
	<header></header>
	<main></main>
	<footer></footer>
</body>
```
## 移动端实现
在开发移动端时，可能会有点击某个按钮，显示全屏遮罩层，如果内容过多，则出现滚动条，关闭按钮在最下面。这种需求刚好符合`Stciky footers`。遮罩层，我们一般常使用fix布局。提供一种增加包裹层实现方式。
遮罩层`position:fixed`，然后包裹层最小高度设为`min-height: 100%`，内容容器设置`padding-bottom: 30px`页脚提供位置。页脚采用`margin-top: -30px`来定位，同时注意清除浮动的使用。
``` html
.parent {
	position: fixed;
	z-index: 100;
	top: 0;
	left: 0;
	width: 100%;
	height: 100%;
	overflow: auto;
}
.main-wrapper {
	width: 100%;
	min-height: 100%;
}
.main {
	padding-bottom: 30px;
}
.footer	{
	height: 30px;
	width: 100%;
	position: relative;
	margin-top: -30px;
	clear: both;
}
.clearfix:before, .clearfix:after {
	content: "";
	display: table;
}
.clearfix:after {
	clear: both;
}
-------------------
<div class="parent">
	<div class="main-wrapper clearfix">
		<div class="main">
			<p>123</p>
		</div>
	</div>
	<div class="footer">
		123
	</div>
</div>
```

有更多实现技巧或建议，欢迎留言交流~
