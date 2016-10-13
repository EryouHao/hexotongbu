---
title: JavaScript错误类型整理
date: 2016-10-13 20:12:40
tags: 错误类型
categories: JavaScript
---

## Error 
    所有错误的基本类型。引擎从来不会抛出该类型的错误。
<!-- more -->
##  EvalError
    通过 eval() 函数执行代码发生错误时抛出。

## RangeError
    一个数字超出它的边界时抛出，例如创建一个长度为-10的数组（new Arrayrray(-10)）

## ReferenceError
    期望的对象不存在时抛出，例如试图在一个null对象引用上调用一个函数

## SyntaxError
    给 eval() 函数传递的代码中有语法错误时抛出

## TypeError
    变量不是期望的类型时抛出，例如 'str' in true 或 new 10

## URIError
    给 encodeURI()、encodeURIComponent()、decodeURI() 或者 decodeURICommponent() 等函数传递格式非法的URI字符串时抛出