---
title: js设计模式之单例模式[note]
date: 2016-10-10 22:37:26
tags: 设计模式
categories: JavaScript
---

核心：确保只有一个实例，并提供全局访问。

<!-- more -->

## 惰性单例
惰性单例指的是在需要的时候才创建对象实例。

### 通用的惰性单例
``` javascript
var getSingle = function (fn) {

    var result;

    return function () {
        return result || (result = fn.apply(this, arguments));
    }
}
```

### 登录浮窗
当然你可以用遮罩层防止多次点击登录按钮来组织事件的触发，这里不谈，来聊聊单例模式实现登录浮窗
``` javascript
var createLoginLayer = function () {
    var div = document.createElement('div');
    div.innerHTML = '我是登录浮窗';
    div.style.display = 'none';
    document.body.appendChild(div);
    return div;
}

var createSingleLoginLayer = getSingle(createLoginLayer);

document.getElementById('loginBtn').onclick = function () {
    var loginLayer = createSingleLoginLayer();
    loginLayer.style.display = 'block';
};
```
### 为渲染列表绑定事件
当然单例模式的用途远不止创建对象，如通常渲染完页面中的一个列表之后，接下来要为这个列表绑定click事件，如果是通过ajax动态往列表里追加数据，在使用事件代理的前提下，click事件实际上只需要在第一次渲染列表的时候被绑定一次，但是我们不想去判断当前是否是第一次渲染列表，通常可能会想到jquery的one事件,如果利用getSingle函数，也能达到一样的效果
``` javascript
var bindEvent = getSingle(function () {
    console.log('绑定事件');
    document.getElementById('div1').onclick = function () {
        alert('click');
    }
    return true;
});

var render = function () {
    console.log('开始渲染列表');
    bindEvent();
}

render();
render();
render();
```
render函数和bindEvent函数都执行了3次，但div实际上只被绑定了1次

来自《JavaScript设计模式与开发实践》的读书笔记，如有更多的干货，欢迎拍砖~。















