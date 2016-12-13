---
title: vue2.0开发模拟项目一点点总结
date: 2016-12-13 12:12:22
tags: vue
---

这是一篇非详细技术总结。如有错误，欢迎指正与吐槽~。
博主原文(http://fehey.com/2016/12/13/vuesell/)

## 所用资源
> 1. vue2.0
2. vue-resource
3. vue-router
4. better-scroll
5. webpack
6. eslint
7. stylus

## vue-resource

首先要说的是，数据咋来，现在越来越多的数据传输方式都是json数据格式，包括用jquery开发时，也有很好用的$.ajax来进行数据请求与处理，那么vue-resource提供了一种类似的，并且api更加简洁易用，压缩后文件更小。配合ES 6的Lambda写法，更加优雅

这里提供对照着一个实例来顺一下：
``` javascript
this.$http.get('/api/seller').then((response) => {
  response = response.body;
  if (response.errno === ERR_OK) {
    this.seller = Object.assign({}, this.seller, response.data);
  }
});
```
通过 `this.$http.get` 来定义通过vue实例来发送get请求，然后通过then后面的回调函数将请求成功的数据接收，通过状态码来判断是否成功以及复制给vue的数据对象。由于这里是用的mock数据（模拟后台数据），所以用的模拟状态码。

同时，这里省略了`errorcallback`的定义，正常开发中需要进行定义，甚至可以利用vue-resource的`inteceptor`进行体验优化，比如定义请求时的loading动画界面。在vue中即可以提取出loading组件。
## Object.assign()
在这里科普一下`Object.assign()`的用法。官方解释为：可以把任意多个的源对象自身的可枚举属性拷贝给目标对象，然后返回目标对象。
``` javascript
Object.assign(target, ...sources)
```
``` javascript
this.seller = Object.assign({}, this.seller, response.data);
```
对应到上面的实例，即将`vm.seller`属性和请求返回数据对象合并到空对象，然后赋值给`vm.seller`这里加上`this.seller` 即提供了一种可扩展的机制，倘若原来的属性中有预定义的其他属性。这种写法比较常见，比如写js时自定义一个方法，ES6之前传参设置默认值（ES6中允许函数参数设置默认值，更加便捷）。

另外需要注意的是`Object.assign()`方法只会拷贝源对象自身的并且可枚举的属性到目标身上。也就意味着继承属性和不可枚举属性是不能拷贝的，而且拷贝是对象的属性的引用而不是对象本身。

## vue-router

那接下来聊聊页面路由，vue提供了一个`vue-router` ,顾名思义，这个东西就是用来进行路由分发的。

通过一个简单的示例来理解一下：
``` html
<!-- 导航 -->
<router-link to="/home">home</router-link>
<router-link to="/about">about</router-link>

<!-- 路由出口 组件渲染容器 -->
<router-view></router-view>
```
``` javascript
// 定义路由
const routes = [
  { path: '/home', component: home },
  { path: '/about', component: about }
];
// 创建 router实例，将路由配置传入
const router = new VueRouter({
  routes: routes
});
// 挂载
new Vue({
  el: '#app',
  template: '<App/>',
  router: router,
  components: { App }
});
```
vue 通过 `to` 属性来指定链接，导航点击时组件渲染容器就会发生相应的变化，渲染不同的组件。这对于简单的单页面应用已经够用了，在此阶段还没有用过 `vuex` ，这个好像应对复杂的单页面进行状态管理更优雅一些。

## 组件间通讯
vue是进行组件式开发，故组件间通讯是必不可少的。vue提供了一种方式，即在子组件定义`props`来传递父组件的数据对象。
``` html
<!-- App.vue -->
<v-header :seller="seller"></v-header>
```
``` javascript
// header.vue
props: {
  seller: {
    type: Object
  }
}
```
像上面这样就可以在 header 组件中使用seller对象了。
那么如果子组件想传到父组件呢？
``` javascript
// food.vue 子组件
this.$emit('eventCartadd', event.target);
```
``` html
<!-- goods.vue 父组件 -->
<food v-on:eventCartadd="_drop"></food>
```
事件派发： `vm.$emit` 附加参数传给监听器回调


## 组件提取管理
以前进行pc端网页开发时可能采取过模块化开发，对页面进行模块划分，然后尽量提高复用性与可维护性，以前大致写过一篇粗浅的文章[一个简单的电商网站模块化开发总结](http://fehey.com/2016/10/14/100u/)
那现在用vue进行组件式开发，将组件化开发表现到极致。现在，页面结构拆分分析就是极其重要的一环。

vue开发中会有一个`src/components`目录,这里是存放vue组件的，即`**.vue`文件，组件提取拆分的原则简单的可以理解为把一块儿内容可复用，把样式、功能相近的区块抽象成一个组件，另外组件中用到的图片等资源就近维护，即可以考虑在组件文件夹中新建images文件夹。

另外如果项目复杂，也可以更深层次的拆分，比如将比较通用、不包含业务逻辑的*基础组件*单独管理，将*业务组件* 放在components进行管理。当然也不是组件存放拆分的越细越好，如果只是十几个二十几个组件，存放管理的过细的话，同样增加了目录结构的管理，可以考虑直接同级存放。

另外抽离组件时要尽量遵循单一职责原则，复用性更高，不要设置额外的margin等影响布局的东西。

## CSS预处理器
可能有很多人在说CSS预处理器的不好，增加了学习成本，本末倒置，css使用已经很艰难了，哪有力气学习预处理器。

我个人观点是，它可以更好的帮助开发，提升开发效率，节省编写css时的代码量，当然直接用css也能实现，不过当遇上组件化开发时，会发现有很沉重css代码量，那利用预处理器的mixin，函数等可以更高效的解决一些问题。况且学习成本并没有想象中的那么高，网上不经常有很多10分钟入门less之类的文章，其实掌握了基本的用法就够用了。

这里举一个栗子🌰：
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

利用stylus可以很方便的写一个一像素边框

## 关于eslint
`eslint` 是一个js代码风格检查器，配合vue-cli脚手架中的热更新，可以很方便的定位和提示错误。很多新手对于频繁报错很是不爽，很急躁，感觉总是去处理这些错误，耽误开发时间，觉得有这些时间多去学点vue多好。

我的观点是，使用eslint是对开发有好处的，且不说在公司多人协作开发时代码风格可以保持一致，可以很方便的阅读他人的代码。单就个人开发的话，自己写的也会越来越规范，代码也越来越漂亮。想一想写一手漂亮的代码是多么愉快的事情。况且那些报错都有对应的官网链接也有英文提示，可以翻译也可以去官网查。查几次之后就会发现错误就那么几种，而且自己越写越整齐规范，错误也就逐渐消失了。对于以后的开发或是多人协作是收益很大的。

## 关于其他
### vue2.0踩坑
vue2.0 相较 1.0 精简了很多api，例如：

*1. $index废除* *2. transition过渡状态变为4种状态* *3. v-el废除* 。具体的可以移步官方文档[传送门](https://vuefe.cn/v2/guide/migration.html)

### better-scroll

一个超级好用的移动端滚动插件，github：(https://github.com/ustbhuangyi/better-scroll)

### 手机测试网页技巧

将localhost换成自己的ip，windows在命令行执行ipconfig查看，mac执行ifconfig查看。

然后复制地址栏地址，进入草料二维码网站（cli.im），然后生成二维码，然后用手机扫一扫就可以查看了，前提是，你手机和电脑必须在同一个局域网。

### css书写顺序

先写display（布局的），宽高（位置的，影响重绘的），颜色（可被继承的）

### github
github:(https://github.com/EryouHao/vue-sell/)
