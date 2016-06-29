---
title: gulp的初阶使用
date: 2016-06-29 19:15:17
tags: gulp
categories: 前端工具
---
安装好gulp之后接下来就是使用了，此文主要介绍一些前端开发时常用的一些插件及其用法，并没有写复杂的task，所有均为示例
<!-- more -->
## 插件安装
>安装本地服务器插件：`cnpm install gulp-connect --save-dev`
 安装合并文件插件：`cnpm install gulp-concat --save-dev`
 安装压缩js文件插件：`cnpm install gulp-uglify --save-dev`
 安装重命名插件：`cnpm install gulp-rename --save-dev`
 安装压缩css文件插件：`cnpm install gulp-minify-css --save-dev`
 优化图像文件的尺寸，在不改变图像质量的情况下，最小化图像大小：
 `cnpm install gulp-imagemin --save-dev`

## 插件引入
在gulpfile.js的顶部引入
``` bash
    var gulp = require('gulp');//将gulp插件包含进来
    var sass = require('gulp-sass'); //包含sass转换为css插件
    var less = require('gulp-less'); //包含less转换为css插件
    var connect = require('gulp-connect'); //包含服务器插件
    var concat = require('gulp-concat');//合并文件
    var uglify = require('gulp-uglify');//压缩js
    var rename = require('gulp-rename');//重命名文件
    var minifyCSS = require('gulp-minify-css');//压缩css
    var imagemin = require('gulp-imagemin');//优化图片
```
## 插件用法
1.复制文件到指定文件夹
``` bash
    //复制文件到指定文件夹
    gulp.task('copy-index',function(){
        return gulp.src('index.html')
            .pipe(gulp.dest('dist'))
            .pipe(connect.reload());
    });
```
2.复制图片到指定文件夹
``` bash
    gulp.task('images',function(){
        return gulp.src('images/*.jpg')
            //.pipe(imagemin()) //未成功
            .pipe(gulp.dest('dist/images'));
            //images/**/* images目录下的所有子目录和所有东西(包含东西最多)
            //images/*/*  images目录下的东西和子目录下的东西
            //images/*.{png,jpg} images目录下的所有以png和jpg为后缀名的图片
    });
```
3.多个数据文件复制(后面的目录可以不存在，不存在的话会自动创建)，在前面加叹号是排除这个（类）文件
``` bash
    gulp.task('data',function(){
        return gulp.src(['xml/*.xml','json/*.json','!json/secre-*.json']).pipe(gulp.dest('dist/data'));
     });
  ```
4.创建依赖任务
``` bash
    gulp.task('build',['copy-index','images','data'],function(){
        console.log('编译成功！');
    });
```
5.创建监听任务
``` bash
    gulp.task('watch',function(){
    //当文件发生变化时，自动执行相应的任务
        gulp.watch('index.html',['copy-index']);
        gulp.watch('images/**/*.{jpg,png}',['images']);
        gulp.watch(['xml/*.xml','json/*.json','!json/secre-*.json'],['data']);
    });
```
6.将sass文件编译成css
``` bash
    gulp.task('sass',function(){
        return gulp.src('stylesheets/**/*.scss')
            .pipe(sass())  //上面定义的名字 var sass
            .pipe(gulp.dest('dist/css')); 
    });
```
7.将less文件编译成css
``` bash
    gulp.task('less',function(){
        return gulp.src('stylesheets/**/*.less')
            .pipe(less())  //上面定义的名字 var sass
            .pipe(minifyCSS())  //最小化css，去掉了注释和多余的空格
            .pipe(gulp.dest('dist/css')); 
    });
```
8.设置默认任务
``` bash
    //退出任务监听：Ctrl + C  然后  Y
    //默认任务 gulp
    gulp.task('default',['server','watch']);
```
9.合并js、压缩js、重命名压缩后的js
``` bash
    gulp.task('scripts',function(){
        return gulp.src(['javascripts/jquery.js','javascripts/index.js'])
            .pipe(concat('vendor.js'))  //合并js文件
            .pipe(gulp.dest('dist/js'))
            .pipe(uglify()) //最小化一下合并后的文件
            .pipe(rename('vendor.min.js')) //重命名压缩后的js文件
            .pipe(gulp.dest('dist/js'));
    });
```
10.设置一个本地服务器
``` bash
    gulp.task('server',function(){
        connect.server({
            root:'dist',      //服务器的根目录
            livereload: true  //启用实时刷新的功能
        });
    });
```
