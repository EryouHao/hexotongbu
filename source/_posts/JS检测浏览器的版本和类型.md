---
title: JS检测浏览器的版本和类型
date: 2016-06-25 10:41:10
tags: javascript
categories: 前端说
---
在做项目中有用到只用火狐浏览器使用的场景，所以用js检测浏览器的类型，然后进行拦截。
<!-- more -->
### 检测浏览器的类型

``` javascript
<script type="text/javascript"> 
 var isChrome = window.navigator.userAgent.indexOf("Chrome") !== -1; 
    alert(isChrome);
 if (isChrome) { 
    alert("是Chrome浏览器"); 
 } else 
 { 
    alert("不是Chrome浏览器"); 
 }
 </script>﻿​
 ```
 ### 检测浏览器的版本

 ``` javascript
 <script type="text/javascript">
 var browser = navigator.appName
 var b_version = navigator.appVersion
 var version = b_version.split(";");
 var trim_Version = version[1].replace(/[ ]/g, "");
 
 if (browser == "Microsoft Internet Explorer"
 && (trim_Version == "MSIE6.0" || trim_Version == "MSIE7.0" || trim_Version == "MSIE8.0")) {
 location="unuseful.html";
 
 }
 </script>
 ```