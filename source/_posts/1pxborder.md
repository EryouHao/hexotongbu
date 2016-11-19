---
title: 移动端1像素border实现技巧
date: 2016-11-19 18:44:52
tags: 移动端
---
在pc端时，要想实现一像素边框可以直接设置 `border: 1px solid #ccc;` 即可，但是在移动端，由于you设备像素比的存在，所以，不同设备的表现可能不一致，比如iPhone6就可能表现为2像素，由于此篇只是针对1像素实现，故想了解设备像素比的概念，请移步张鑫旭大神的blog[设备像素比devicePixelRatio简单介绍](http://www.zhangxinxu.com/wordpress/2012/08/window-devicepixelratio/)
<!-- more -->

## 实现原理
利用css3的Y轴缩放转换 `transform: scaleY()` 、伪类和媒体查询 `@media (min-device-pixel-ratio: 2)`来实现。

## 实现示例

``` css
.box {
	width: 100px;
	height: 100px;
	background: #ccc;
	position: relative;
}
.box:after {
	content: '';
	width: 100%;
	position: absolute;
	left: 0;
	bottom: 0;
	border-top: 1px solid #000;
}
@media (min-device-pixel-ratio: 2) {
	.box:after {
		transform: scaleY(0.5);
	}
}
@media (min-device-pixel-ratio: 1.5) {
	.box:after {
		transform: scaleY(0.7);
	}
}
```
这样，便实现了1像素的下边框，媒体查询是根据设备的像素比，如果是2，则缩放0.5，相乘之后还是1px，如果是1.5的话，则缩放0.7，近似为1px。以上代码没有`-webkit-` 兼容，用到的可自行添加。

## Stylus 写法
如果项目中多处用到1px，那么将它写成公共样式再合适不过了。那如果，有多处1px并且颜色也有差异，这时候，用一种css预处理器来写一个函数就更好了。下面提供一种stylus写法,另外也可以用less或sass等。
``` stylus
border-1px($color)
  position: relative
  &:after
    display: block
    position: absolute
    left: 0
    bottom: 0
    width: 100%
    border-top: 1px solid $color
    content: ' '
@media (-webkit-min-device-pixel-ratio: 1.5),(min-device-pixel-ratio: 1.5)
  .border-1px
    &::after
      -webkit-transform: scaleY(0.7)
      transform: scaleY(0.7)

@media (-webkit-min-device-pixel-ratio: 2),(min-device-pixel-ratio: 2)
  .border-1px
    &::after
      -webkit-transform: scaleY(0.5)
      transform: scaleY(0.5)
```
如有更好的技巧，欢迎留言，交流~
