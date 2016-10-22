---
title: 事件冒泡、事件捕获、事件委托
date: 2016-10-20 10:40:53
tags: js事件
categories: javascript
---


# 事件冒泡
事件冒泡是指当给子元素绑定事件后，触发事件时，会层层向外冒泡，直到document。
<!-- more -->
例如：
``` html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>事件冒泡</title>
</head>
<body id='body'>
    <div id="par">
        123
        <div id="sun">456</div>
    </div>
    <script>
        window.onload = function () {
            var par = document.getElementById('par'),
                sun = document.getElementById('sun');
                body = document.getElementById('body');

            sun.addEventListener('click', function (e) {
                console.log('我是子元素事件');
                e.stopPropagation();
            }, false);

            par.addEventListener('click', function () {
                console.log('我是父元素事件');
            }, false);
            body.addEventListener('click', function () {
                console.log('我是body事件')
            }, false);
            document.addEventListener('click',function() {
                console.log('我是document事件');
            }, false);
        }
    </script>
</body>
</html>
```
当点击id为par的div时，会逐层冒泡，顺序为：`div -> body -> document`
当然也可以用`stopPropagation()`方法来阻止冒泡。
# 事件捕获
另外还有一种事件机制是事件捕获，和事件冒泡触发顺序相反，即从最外层逐层向里找，直到找到目标元素,没错即`document -> body -> div`
即将addEventListener第三个参数赋为true。
``` javascript
body.addEventListener('click', function () {
    console.log('我是body事件')
}, true);
```

当捕获和冒泡同时存在时，遵循先捕获后冒泡原则。
同样，`stopPropagation()`也能阻止捕获。

# 事件委托
理解了上面两个原理之后，现在来聊一聊事件委托。
我们做开发时，经常会遇到为列表中所有元素来绑定事件，例如：
``` html
<ul id="todo">
    <li>123</li>
    <li>456</li>
    <li>789</li>
</ul>
```
当然，可能会有人采用for循环，来为列表中的每一个li来绑定监听事件，如果列表元素少，还可以接受，但如果一多，100多个，甚至几百个，很显然，开销将是很大的。有没有一种优雅的解决办法呢？当然！事件委托就是。

大致意思就是，将本来自身绑定的事件，委托给其他元素，一般为父元素。例如上面的例子，完全可以委托给ul来监听。例如：
``` javascript 
var todo = document.querySelector('#todo');
todo.addEventListener('click', function (ev) {
    var t = ev.srcElement || ev.target; //捕获事件源，IE下有srcElement属性，chrome等有target
    if (t.tagName === 'LI') {
        t.parentNode.removeChild(t);
    }
    ev.preventDefault(); // 阻止默认事件

}, false);
```
这样只需一次绑定即可。
事件委托除了可以解决这个问题，另外也可以解决动态dom的事件绑定问题。下面附一个小例子，可以查看源码加深理解下：[一个例子带你认识事件委托](http://fehey.com/demos/html/shijianweituo.html)

