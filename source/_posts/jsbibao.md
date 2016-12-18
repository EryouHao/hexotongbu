---
title: js不完全入门之闭包
date: 2016-12-18 21:31:11
tags: javascript
---
闭包是基于词法作用域书写代码时所产生的自然结果，你甚至不需要为了利用它们而有意识地创建闭包。

当函数可以记住并访问所在的词法作用域时，就产生了闭包，即使函数是在当前词法作用域之外执行。

## 一个经典的示例

``` javascript
function foo() {
    var a = 2;

    function bar() {
        console.log(a);
    }

    return bar;
};

var baz = foo();
baz(); // 2  ---- 闭包
```

解释：函数 `bar()` 的词法作用域能够访问 `foo()` 的内部作用域。然后将 `bar()` 函数本身当做一个值类型进行传递，这个在JavaScript中是被允许的。在 `foo()` 执行后，返回了一个函数并赋值给baz，当 `baz()` 执行后，即调用了 `bar()` 函数。

即在这个例子中，`bar()` 在自己定义的词法作用域之外被执行。而且执行时的词法作用域是定义时的词法作用域，即可以正常访问到变量`a`。

一般情况下，由于函数作用域的存在，js的引擎在函数执行完之后便会回收定义变量等造成的开销，而闭包恰恰可以组织这件事。`bar()` 依然持有对该作用域的引用，而这个引用就叫做闭包。

## 其他几种方式

``` javascript
function foo() {
    var a = 2;

    function baz() {
        console.log(a);
    }

    bar(baz);
}

function bar(fn) {
    fn(); // ---- 闭包
}
```

解释： 将内部函数 `baz()`传递给 `bar(fn)`，然后fn() 执行时，即 `baz()`执行。即在词法作用域之外被执行。

``` javascript
var fn;

function foo() {
    var a = 2;

    function baz() {
        console.log(a);
    }

    fn = baz; // 将baz赋值给fn变量
}

function bar() {
    fn(); // ---- 闭包
}

foo();

bar(); // 2
```
解释： 将 `baz` 赋值给外部变量 `fn`，然后外部函数通过 `fn()`来调用。即在词法作用域之外被执行。

## 循环和闭包
在执行 for循环时，我们常常会引用循环变量i（只是一个表示），假如像这样:
``` javascript
for (var i = 1; i <= 5; i++) {
    setTimeout(function timer() {
        console.log(i);
    }, i * 1000);
}
```
我们想输出：1，2，3，4，5。然后却输出：6，6，6，6，6。为什么呢？

输出的是循环结束时i的值。因为延迟函数的回调会在循环结束时才执行。我们想在每次迭代时“捕获”一个，但是根据作用域，尽管循环中的五个函数是在各个迭代中分别定义的，但是它们都被封闭在一个共享的全局作用域中，因此实际上只有一个i，即所有函数共享一个i的引用。

所以，我们需要在循环中每个迭代都创建一个闭包作用域。

>1.通过IIFE

``` javascript
for (var i = 1; i <= 5; i++) {
    (function(j) {
        setTimeout(function timer() {
            console.log(j);
        }, j * 1000);
    })(i);
}
```

>2.通过块作用域

``` javascript
for (let i = 1; i <= 5; i++) {
    setTimeout(function timer() {
        console.log(i);
    }, i * 1000);
}
```
不得不说ES6 中新增的let真是好用，使得块作用域和闭包联手，6到飞起。

## 写在后面
其实闭包的作用有很多，比如常见的模块化编程中就有用到，通过暴露函数接口，然后在词法作用域外部进行调用。
``` javascript
var foo = (function MyModule(){
    var a = 1,
        b = 2;

    function show() {
        console.log(a);
    }
    // ...
    return {
        show: show
        // ...
    };
})();

foo.show(); // 1;
```
模块模式需要有两个必要条件：
>1.必须由外部的封闭函数，并且至少被调用一次（每次调用都会创建一个新的模块实例）。
>2.封闭函数必须返回至少一个内部函数，这样内部函数才能在私有作用域中形成闭包，并且可以访问或者修改私有的属性。
