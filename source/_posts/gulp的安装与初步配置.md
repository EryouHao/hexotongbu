---
title: gulp的安装与初步配置
date: 2016-06-25 10:49:44
tags: gulp
categories: 前端工具
---
听说gulp是基于流的自动化构建工具，一直以来都是野生前端，所以今天拿来试试看，好像还不错的样子。此篇为安装配置篇，后续有初阶使用篇和gulp-file-include的使用篇。
<!-- more -->

### 1.安装node.js
从官网下载适合自己系统的node.js，然后一路下一步点击安装就可以了（也可以自己设置安装路径）。
    检测安装是否成功：
    点击`widows+R`调出命令行
    输入 `node -v`回车，如果安装成功会显示版本信息
    输入`npm -v` 回车，同上。
### 2.选装cnpm
命令提示符执行：
``` bash
npm install cnpm -g --registry=https://registry.npm.taobao.org
```
### 3.全局安装gulp
执行
``` bash
cnpm install gulp -g
```
执行`gulp -v` 检测是否安装成功
### 4.创建package.json文件
进入到项目目录执行cnpm init 创建 package.json 文件
>name:项目名称（必填）
version:项目版本（必填）
description:项目描述（必填）
entry point：入口文件
test command：测试命令
git repository：git地址
keywords：关键字
author：作者信息
license：许可协议

看到yes后继续回车，然后查看目录就可以看到文件了。
### 5.本地安装gulp插件
进入到项目目录后执行
``` bash
cnpm install gulp --save-dev
```
### 6.新建gulpfile.js
gulpfile.js是gulp项目点额配置文件
编辑文件输入
``` javascript
var gulp = require('gulp');
gulp.task('hello',function(){
    console.log('你好！');
});
```
然后打开命令行执行：`gulp hello`命令看到出现你好字符就OK了。











    