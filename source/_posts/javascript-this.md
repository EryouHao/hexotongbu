---
title: JavaScript不完全入门之this
date: 2016-08-22 12:57:35
tags: this
categories: JavaScript
---
早有耳闻JavaScript中this难懂，此篇为入门篇，并没有列举很复杂的示例，大部分为解释名词所列。如有错误，欢迎指正。
<!-- more -->

# 全局的this（浏览器）

``` javascript
  console.log(this.document === document);//true
  console.log(this === window); //true

  this.a = 37;
  console.log(window.a); //37
```

# 一般函数的this（浏览器）
``` javascript
  function f1(){
    return this;
  }
  f1() === window; //true,global object
  注：this指向全局对象

  function f2(){
    "use strict"; //see strict mode
    return this;
  }
  f2() === undefined; //true
```
  注：严格模式下this指向undefined

# 作为对象方法的函数的this
``` javascript
  var o = {
    prop: 37,
    f: function(){
      return this.prop;
    }
  };
  console.log(o.f()); //logs 37

  var o = {prop: 37};
  function independent(){
    return this.prop;
  }
  o.f = independent;
  console.log(o.f()); //logs 37
```

# 对象原型链上的this
``` javascript
  var o = {
    f:function(){
      return this.a + this.b;
    }
  };
  p.a = 1;
  p.b = 4;
  console.log(p.f()); //5
```
  注：p的原型是o，p.f()调用的是原型链上的o的方法属性

# get/set方法与this
``` javascript
  function modulus(){
    return Math.sqrt(this.re * this.re + this.im * this.im);
  }
  var o = {
    re: 1,
    im: -1,
    get phase(){
      return Math.atan2(this.im,this.re);
    }
  };
  Object.defineProperty(o,'modulus',{
    get:modulus,enumerable:true,configurable:true
  });
  console.log(o.phase,o.modulus); //logs -0.78 1.4142
```

# 构造器中的this
``` javascript
  function MyClass(){
    this.a = 37;
  }

  var o = new MyClass();
  console.log(o.a); //37
```
  注：当只创建一个函数时，this指向全局对象window，
     当用new创建一个对象时，对象调用的为原型链上的属性
     this指向MyClass.prototype这样一个空对象
``` javascript
  function C2(){
    this.a = 37;
    return {a: 38};
  }

  o = new C2();
  console.log(o.a); //38
```
  注：当函数构造器有return并且返回一个对象时，那么
     new的对象调用属性时为return的对象，而不会指向this

# call/apply方法与this
``` javascript
  function add(c,d){
    return this.a + this.b + c + d;
  }

  var o = {a: 1, b: 3};

  add.call(o,5,7); //1+3+5+7 = 16

  add .apply(o,[10,20]); //1+3+10+20 = 34
```
  注：call和apply基本没区别，一个是传基本参数，一个是传数组
``` javascript
  function bar(){
    console.log(Object.prototype.toString.call(this));
  }
  bar.call(7); // "[object Number]"
```

# bind方法与this
``` javascript
  function f(){
    return this.a;
  }

  var g = f.bind({a: "test"});
  console.log(g()); //test

  var o = {a: 37, f: f, g: g};
  console.log(o.f(), o.g()); //27 test
```
  注：一般函数的调用 o.f() 会将对象的属性传入，即this指向了a: 37这个属性
     当用bind绑定之后，再次调用g()会按照之前绑定后的走

 判断this的指向（1-4优先级逐级递减，1优先级最高，4优先级最低）：
>1、函数被new调用，this指向由new新构造出来的这个对象；
2、函数通过call()、apply()、bind()调用，this指向被绑定的对象；
3、函数作为方法被调用，this指向这个对象（即常说的对象上下文）；
4、默认（非严格模式）情况下，this指向window,  严格模式下，this指向undefined。
