---
title: Vue+MySQL+Express小试牛刀
date: 2017-01-20 08:56:24
tags: vue javascript
categories: JavaScript
---
眼睛一闭一睁，到年底了。早就想尝试用js玩儿全栈，最近正好做毕设了，是一个关于资金的管理系统，于是乎，正好用来练练手。一咬牙一跺脚----嗯，就用Vue+MySQL+Express吧。前端用Vue，后端用Express做服务端提供数据接口，数据库用MySQL（由于毕设就要求的是基于SQL数据库）。

说时易，做时难。以前都是单写Vue，数据请求是请求的假数据，而且没有搭建过环境，所以单独用Express的话，也只是用`express-generator`生成expreess项目，而且前端是基于`jade`或`ejs`模板的。思来想去不知道如何将Vue加进去，如果要用Vue并且以组件式开发，则必然要用到`webpack`编译打包，于是...

## 1.用`vue-cli`脚手架工具创建一个基于webpack的Vue项目

前提：安装node环境

首先全局安装： `npm install -g vue-cli`,然后创建一个项目文件夹，进入项目目录执行：`vue init webpack my-project`(my-project是项目名)。安装完成之后可以执行 `npm install` 安装依赖，执行 `npm run dev`进行开发时调试。

安装 `vue-resource`依赖,一种安装方式先在package.json中对应地方添加，然后执行`npm install`
``` json
"dependencies": {
    "vue": "^2.1.0",
    "vue-router": "^2.0.3",
    "vue-resource": "^1.0.3"
  },
```
## 2.添加Express服务端目录。

前提：电脑安装mysql数据库

在项目根文件夹下创建一个server文件夹。然后里面创建下面三个文件：

db.js----用来添加mysql配置
``` javascript
// 数据库连接配置
module.exports = {
	mysql: {
		host: 'localhost',
		user: 'root',
		password: '',
		database: 'test',
		port: '3306'
	}
}
```
index.js----Express服务器入口文件
``` javascript
// node 后端服务器

const userApi = require('./api/userApi');
const fs = require('fs');
const path = require('path');
const bodyParser = require('body-parser');
const express = require('express');
const app = express();

app.use(bodyParser.json());
app.use(bodyParser.urlencoded({extended: false}));

// 后端api路由
app.use('/api/user', userApi);

// 监听端口
app.listen(3000);
console.log('success listen at port:3000......');
```
sqlMap.js----SQL语句映射文件，以供api逻辑调用
``` javascript
// sql语句
var sqlMap = {
	// 用户
	user: {
		add: 'insert into user(id, name, age) values (0, ?, ?)'
	}
}

module.exports = sqlMap;
```
api/userApi.js ---- 测试用api示例
``` javascript
var models = require('../db');
var express = require('express');
var router = express.Router();
var mysql = require('mysql');
var $sql = require('../sqlMap');

// 连接数据库
var conn = mysql.createConnection(models.mysql);

conn.connect();
var jsonWrite = function(res, ret) {
	if(typeof ret === 'undefined') {
		res.json({
			code: '1',
			msg: '操作失败'
		});
	} else {
		res.json(ret);
	}
};

// 增加用户接口
router.post('/addUser', (req, res) => {
	var sql = $sql.user.add;
	var params = req.body;
	console.log(params);
	conn.query(sql, [params.username, params.age], function(err, result) {
		if (err) {
			console.log(err);
		}
		if (result) {
			jsonWrite(res, result);
		}
	})
});

module.exports = router;
```

编写完成之后，就可以在项目根目录下安装依赖`npm install express mysql body-parser`;

此时在server文件夹下执行`node index`（这里也可以加载package.json中，然后使用npm执行）看到success listen at port:3000......即服务端启动成功。

## 3.编写vue测试文件

由于这里只是为了测试，所以直接在vue-cli生成的Hello.vue中编写即可。

``` html
<template>
  <div class="hello">
    <h1>{{ msg }}</h1>
    <form>
      <input type="text" name="username" v-model="userName"> <br>
      <input type="text" name="age" v-model="age"> <br>
      <a href="javascript:;" @click="addUser">提交</a>
    </form>
  </div>
</template>

<script>
export default {
  name: 'hello',
  data () {
    return {
      msg: 'Welcome to Your Vue.js App',
      userName: '',
      age: ''
    }
  },
  methods: {
    addUser() {
      var name = this.userName;
      var age = this.age;
      this.$http.post('/api/user/addUser', {
        username: name,
        age: age
      },{}).then((response) => {
        console.log(response);
      })
    }
  }
}
</script>
```
## 4.设置代理与跨域

完成上面3步之后，执行`npm run dev`，然后输入一组数据，点击保存，你会发现会报一个错误：vue-resource.common.js?e289:1071 POST http://localhost:8080/api/user/addUser 404 (Not Found).
这是由于直接访问8080端口，是访问不到的，所以这里需要设置一下代理转发映射.

vue-cli的config文件中有一个`proxyTable`参数，用来设置地址映射表，可以添加到开发时配置（dev）中
``` javascript
dev: {
	// ...
	proxyTable: {
        '/api': {
            target: 'http://localhost:3000/api/',
            changeOrigin: true,
            pathRewrite: {
                '^/api': ''
            }
        }
    },
	// ...
}
```
即请求`/api`时就代表`http://localhost:3000/api/`，
`changeOrigin`参数接收一个布尔值，如果为`true`，这样就不会有跨域问题了。

好了，到这里，基本上完成了，可以去添加一个数据试试了。

最终项目目录结构
``` 
|-- build
|-- config
|-- node_modules
|-- server
  |-- api
    |-- userApi.js
  |-- db.js
  |-- index.js
  |-- sqlMap.js
|-- src
  |-- assets
  |-- components
    |-- Hello.vue
  |-- App.vue
|-- static
|-- .babelrc
|-- .editorconfig
|-- .gitignore
|-- index.html
|-- package.json
|-- README.md
```
![结果图](https://sfault-image.b0.upaiyun.com/346/559/3465593805-588182172b67b_articlex)
终于调通了，这里的项目目录还可以根据项目的实际情况进行模块划分等等。如有问题或其他建议，欢迎交流~