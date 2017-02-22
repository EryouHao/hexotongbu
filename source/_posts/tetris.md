---
title: 从俄罗斯方块来聊一聊面向对象
date: 2017-02-15 09:48:41
tags: 前端 JavaScript
---
> 迟到的情人节，一起来玩儿一个属于程序员的俄罗斯方块吧！[Tetris belongs to a programmer](http://fehey.com/tetris/)

> github地址：(https://github.com/EryouHao/tetris)

## 面向对象初窥

在许多后端语言中，在基础部分，经常说面向对象很重要，比如在java中有类的概念，封装、继承、多态往往被称为面向对象的三个核心。简单来说，封装就是为了更好的复用与继承。在JavaScript中，在ES6之前虽然没有类的概念，
但往往封装一些工具类，增强其复用性与便利性是一个不错的选择。

曾有句经典的话：万事万物皆对象。在许多面向对象的讲授中，可能很多的都拿人做例子，一个人是一个对象，对象有一些属性（姓名、年龄、性别等等），对象有一些方法（吃饭、行走、说话），整个人类即一个类。很多情况下，通常new一个人类，即创建了一个具体的对象。那拿到面向对象编程来说的话，这个对象可以是更为抽象的概念。

在JavaScript中，也可以通过`new`关键字来创建一个对象的实例。比如
``` javascript
function Person(name,age) {
  this.name = name;
  this.age = age;
}

var zhangsan = new Person('张三', 18);
console.log(zhangsan.name); // "张三"
console.log(zhangsan.age); // 18
```
上面代码即通过`new`关键字创建了一个对象，这个对象即有两个属性`name`,`age`。

通常，一次new，便创建一个实例对象，对象也肯定会存在一些方法吧？对，我们当然可以添加上方法，像下面这样：
``` javascript
function Person(name, age) {
  this.name = name;
  this.age = age;

  this.say = function (msg) {
    console.log(msg);
  }
}

var zhangsan = new Person('张三', 18);
zhangsan.say('hello'); // "hello"
```
这种方法虽然能实现，那由于通过构造函数生成的实例所拥有的的方法是指向函数的索引，如果存在多个实例的话，就会加大内存开销，所以，一般我们定义到原型上。
``` javascript
function Person(name, age) {
  this.name = name;
  this.age = age;
}

Person.prototype = {
  constructor: Person,
  say: function (msg) {
    console.log(msg);
  },
  _programme: function () {
    console.log('内部方法');
  }
}
var zhangsan = new Person('张三', 18);
zhangsan.say('hello'); // "hello"
```
这样当有多个实例时，指向的是一个函数索引。当然，也可以不通过原型，而通过指向外部函数也可以。属性一般都是每个实例都有自身的一个副本，故直接定义。注意，若向上面所示，以重写原型对象的方式来挂载函数属性的话，将改变`prototype`对象的`constructor`属性。虽然这个属性一般没什么具体的作用，但是为了维持惯例，最好尽量指向构造函数。

另外，面向对象的编程，很大程度上是利用上`this`，通过`this`就可以访问实例对象上的属相和原型上的方法。在模块化编程时更方便的调用。

## 自执行函数

在编写复杂应用时，通常需要尽量保持全局命名空间不被污染，自执行函数是其中一种。把代码封装在一个匿名函数中并立刻自行调用，这样，该函数中的所有`var`定义的变量都是局部的，并在函数返回时被销毁（前提是不属于闭包），下面是一个示例：
``` javascript
(function (window) {
  'use strict';

  function Score() {
    this.canvas = new Canvas('score', 100, 70);
    this.score = 0;

    this._init();
  }

  Score.prototype = {
    constructor: Score,
    _init: function () {
      this._render();
    },
    _render: function () {
      this.canvas.drawText(this.score);
    },
    addScore: function (value) {
      this.score += value;
      this._render();
      return this.score;
    }
  };

  window.Score = Score;
})(window);
```
通过传入`window`变量，使得`window`由全局变量变为局部变量，这样可以更快的访问window，（另外可以在代码压缩时进行优化，未亲测）。

通过`window.Score = Score;`在浏览器`window对`象上，挂载一个属性，这样就可以被其他函数或模块进行调用，来创建实例，进行属性和方法的访问。比如上面的`Canvas`即为挂载到`window`对象上的属性。

## 俄罗斯方块的实践

利用面向对象的思想结合H5的Canvas就可以来编写一个简易的俄罗斯方块应用，一个应用中可以分为多个对象，比如一个Shape类（一个随机的俄罗斯方块）,当落下一个方块，并且没有到顶部时，我们就新创建一个（`new`一个实例），里面可以有翻转函数（可被键盘事件控制调用），随机函数（生成实例时内部调用），游戏面板又可以根据方块的移动来更新绘制，分数可以根据消除的行进行更新绘制，这样通过对象来调用其属性方法，可以更合理地进行函数的模块划分与封装，使其应用代码更易维护与管理。