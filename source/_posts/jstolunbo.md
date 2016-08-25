---
title: JavaScript实现焦点轮播图思路整理
date: 2016-08-25 17:15:46
tags:
---

轮播，轮播！你们想要的轮播来了，JavaScript实现焦点轮播图思路整理，代码js小白就可以看得懂，关键是思路，一步一步，先考虑实现，再考虑优化。轮播其实不难，demo请移步文章末尾。
<!-- more -->

# 第一步：编写html代码

一个容器，一个图片容器，一个圆点按钮容器，两个左右控制按钮
``` html
<div id="container">
    <div id="list" style="left: -600px;">
      <img src="img/5.jpg" alt="1">
      <img src="img/1.jpg" alt="1">
      <img src="img/2.jpg" alt="1">
      <img src="img/3.jpg" alt="1">
      <img src="img/4.jpg" alt="1">
      <img src="img/5.jpg" alt="1">
      <img src="img/1.jpg" alt="1">
    </div>
    <div id="buttons">
      <span index="1" class="on"></span>
      <span index="2"></span>
      <span index="3"></span>
      <span index="4"></span>
      <span index="5"></span>
    </div>
    <a href="javascript:;" id="prev" class="arrow">&lt;</a>
    <a href="javascript:;" id="next" class="arrow">&gt;</a>
```

# 第二部：编写css代码

大容器设置超出部分隐藏，相对定位，图片容器绝对定位，图片浮动，两种按钮都用绝对定位和z-index
``` css
#container {
      width: 600px;
      height: 337px;
      border-radius: 3px;
      box-shadow: 0 0 10px #ccc;
      overflow: hidden;
      position: relative;
      margin:40px auto;
    }
    #list {
      width: 4200px;
      height: 337px;
      position: absolute;
      z-index: 1;

    }
    #list img {
      width: 600px;
      height: 337px;
      float: left;
    }
    #buttons {
      position: absolute;
      height: 10px;
      width: 100px;
      z-index: 2;
      bottom: 20px;
      left: 250px;
    }
    #buttons span {
      cursor: pointer;
      float: left;
      width: 15px;
      height: 15px;
      border-radius: 50%;
      background: rgba(255,255,255,0.7);
      margin-right: 5px;
    }
    #buttons .on {
      background: #1E90FF;
    }
    .arrow {
      cursor: pointer;
      display: none;
      line-height: 39px;
      text-align: center;
      font-size: 36px;
      font-weight: bold;
      width: 40px;
      height: 40px;
      border-radius: 3px;
      position: absolute;
      z-index: 2;
      top: 148px;
      background-color: rgba(0, 0, 0, 0.3);
      color: #fff;
      text-decoration: none;
    }
    .arrow:hover{
      color: #1E90FF;
    }
    #prev {
      left: 20px;
    }
    #next {
      right: 20px;
    }
    .arrow:hover {
      background-color: rgba(0, 0, 0, 0.7);
    }
    #container:hover .arrow {
      display: block;
    }
```

# 第三步：编写JavaScript代码

重头戏来了，闲话少叙，直接干货。

首先整理一下思路：
	1.箭头切换控制图片的显示
		实际上就是将盛放图片的容器改变left值，移动位置，由于父容器设置的是超出部分隐藏，此时就可以看到一张图片了
	2.无限滚动
		这个效果很多轮播都没有做，如果不做的话，点击最后一张图片的时候会向左回滚到第一张图片，体验不是很好，目前一种比较常用的方式是在图片列表最后增加一张第一张图片，列表最前增加一张最后一张图片，当点击到最后一张的时候，容器显示的最后一张图片和第一张图片是一样的，故效果一样，此时再修改left值即可。
	3.圆点按钮切换控制
		由于按钮有多个，所以需要循环绑定onclick事件，点击时计算点击索引和当前显示图片索引的差值来赋值动画函数传入的图片容器的偏移量的多少
	4.动画函数
		动画函数中通过一段时间来动态修改图片容器的left值，进行滑动动画的模拟实现。（这个效果在css3中已经可以用transition来实现了。）
	5.循环播放
		当页面加载时自动调用动画函数，鼠标移入容器暂停动画，移出继续动画，这种可以设置一个定时器setInterval来调用右箭头的onclick事件来模拟自动播放。当鼠标移入容器时，清除定时器，移出开启定时器。
	6.其他一些点
		①点击箭头或点击圆点按钮时，圆点高亮，写一个圆点高亮函数。
		②当一张图片运动完成之后才可以触发下一次的onclick事件，定义一个变量animated来控制，是否onclick执行。

代码不贴了，具体可以查看<a href="http://fehey.com/demos/html/jstolunbo.html" target="_blank">demo</a>或<a href="https://github.com/EryouHao/demos/blob/gh-pages/html/jstolunbo.html" target="_blank">github源码</a>
