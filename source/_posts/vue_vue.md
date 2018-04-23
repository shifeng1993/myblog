---
title: 【vue2.0实战分享】搭建vue运行环境以及项目目录
categories: vue
date: 2016-10-02
tags:
  - vue
  - 前端
---
## 第一步
#### 安装nodejs
工程级别的项目需求，一般是长期支持的稳定版本，
所以到官网选择LTS版本下载即可。

链接：[https://nodejs.org/zh-cn/](https://nodejs.org/zh-cn/ "https://nodejs.org/zh-cn/")

windows和mac可以傻瓜式安装，按照默认安装即可。
linux安装需编译，请根据不同linux发行版进行百度。

安装完成，通过 `node -v` 来查看当前node版本

注：以下命令有全局标志`-g`，macos开发环境请自觉加上 `sudo`前缀。例：`sudo npm install -g webpack`
#### 安装全局webpack
`npm install -g webpack`

安装完成，通过`webpack -v`来查看当前webpack版本

## 第二步
#### 安装vue-cli脚手架工具
`npm install  -g vue-cli`
## 第三步
#### 通过vue-cli创建项目基础目录

`vue init webpack 工程名字`<工程名字不能用中文>

之后会让你输入一串项目信息以及项目配置。按需输入即可。
## 第四步
#### 安装依赖模块
`cd 工程名字`
`npm install`
#### 给生产环境装的依赖
`npm install vuex vue-router axios qs --save`
- 安装vue运行依赖
1.vuex
2.vue-router
3.axios
4.qs

#### 给开发环境安装的依赖
- 安装sass预编译环境
`npm install node-sass sass-loader --save-dev`
1.node-sass
2.sass-loader

- 安装express环境以及跨域转发模块以及mock假数据模块
`npm install express http body-parser superagent mockjs --save-dev`
1.express
2.http
3.body-parser
4.superagent
5.mockjs

## 第五步
#### 搭建项目目录
- 以下路径形式均为以项目根目录为开始

目录结构一般为了贴近工程需求，会用到mock假数据，跨域代理，sass预编译，组件状态管理，以及组件间状态传递等vue常用需求，下面就教大家纯手动搭建一个工程目录：
#### 创建express服务文件
提供mock假数据以及跨域代理，如必要也可自行npm安装sql数据库进行连接，在此不做示例。

创建`/server/app.js`文件加入如下代码，
```javascript
/****************************定义和引入********************************/
// 引入模块
var http = require('http');
var express = require('express');
var app = express();
var bodyParser = require("body-parser");
var superagent = require('superagent'); //http://visionmedia.github.io/superagent/#response-properties 文档地址
var server = http.Server(app);
var host = 'http://localhost:';
var port = 9999; //设置本地转发服务端口
var Mock = require('mockjs')

/*************************以下为设置和启用*****************************/
// 设置node服务
app.set('port', port);
app.use(bodyParser.urlencoded({
  extended: false
}));
app.use(bodyParser.json());

// 启动服务
server.listen(app.get('port'), function () {
  console.log("服务已经启动，APIhost：" + host + app.get('port'));
});

/******************以下为此服务支持跨域请求********************/
app.all('*', function (req, res, next) {
  res.header('Access-Control-Allow-Origin', '*');
  res.header('Access-Control-Allow-Headers', 'Content-Type, Content-Length, Authorization, Accept, X-Requested-With , yourHeaderFeild');
  res.header('Access-Control-Allow-Methods', 'PUT, POST, GET, DELETE, OPTIONS, PATCH');
  // res.set({
  //   'Content-Type': 'Content-Type:application/json; charset=UTF-8',
  // })
  if (req.method == 'OPTIONS') {
    res.send(200);
  } else {
    next();
  }
});

/******************以下是api公共部分，不用修改***********************/


// 设置后端服务器url
var dbUrl = 'http://localhost:8383'; //这里写你的后端api地址

// form 请求
var form = function (req, res, API, log) {
  var sreq = superagent.post(dbUrl + API);
  sreq.type('form')
  sreq.send(req.body);
  sreq.pipe(res);
  sreq.on('end', function () {
    console.log(log);
  });
}

// json 请求
var json = function (req, res, API, log) {
  var sreq = superagent.post(dbUrl + API);
  sreq.type('json')
  sreq.send(req.body);
  sreq.pipe(res);
  sreq.on('end', function () {
    console.log(log);
  });
}

/******************以下是api部分***********************/


// 代理转发部分


// form请求示例
app.get('/api/machine/init', function (req, res) {
  var API = '/api/v2/machine/init'
  var log = '初始化机器';
  form(req, res, API, log)
});

// json请求示例
app.get('/api/machine/init', function (req, res) {
  var API = '/api/v2/machine/init'
  var log = '初始化机器';
  json(req, res, API, log)
});


// mock假数据部分

// mock示例
app.get('/goods/size', function (req, res) {
  var data = Mock.mock([{
    "skuId": 4,
    "skuSubject": "清湿茶",
    "skuPackageType": "鹰牌",
  }])
  res.send(JSON.stringify(data))
  console.log('goodssize')
})

```

#### 修改src源文件目录
为了提供组件间状态传递，为工程预留出更加清晰合理的目录，做出如下修改：

- 修改`/src/main.js`为如下代码，

```javascript
import Vue from 'vue'
import App from './App'
import router from './router'
import store from './stores'

// 创建vue实例并渲染到app
new Vue({
  router,
  store,
  render: h => h(App)
}).$mount('#app')

```
- 修改`/src/app.vue`为如下代码，

```javascript
<template>
  <div id="app">
    <router-view></router-view>
  </div>
</template>

<script>
  export default {
    name: 'app'
  }
</script>

<style lang="scss">
</style>

```


- 修改`/src/router/index.js`为如下代码，

```javascript
// 引入插件模块
import VueRouter from 'vue-router'
import Vue from 'vue'

// 引入主页面
import Index from '../containers/Index.vue'

// 引入次级页面

// 启用插件
Vue.use(VueRouter)

// 配置
const routes = [
  {
    path: '/',
    component: Index,
    children: []
  }
]

// 传入配置
const router = new VueRouter({
  mode: 'history',
  base: __dirname,
  routes // （缩写）相当于 routes: routes
})

export default router
```


- 创建`/src/containers/Index.vue`为如下代码，

```javascript
<template>
  <div id="container">
    <router-view></router-view>
  </div>
</template>
<script>
  export default {
    name: 'Index',
    data () {
      return {
      }
    },
    components: {
    }
  }
</script>

<style lang="scss" rel="stylesheet/scss" scoped>
  #container {
    height: 100%;
    overflow: auto;
  }
</style>
```

- 创建`/src/stores/actions/index.js`为如下代码，

```javascript
// actions
const actions = {
}

export default actions

```

- 创建`/src/stores/mutations/index.js`为如下代码，

```javascript
// mutations
const mutations = {
}

export default mutations

```

- 创建`/src/stores/states/index.js`为如下代码，

```javascript
// states
const states = {
}

export default states

```
- 创建`/src/stores/index.js`为如下代码，

```javascript
import Vue from 'vue'
import Vuex from 'vuex'
import States from './states'
import Mutations from './mutations'
import Actions from './actions'

Vue.use(Vuex)

// store中的全局变量，只在store中使用
// vuex实例  store

// vuex 模块
const index = {
  state: States,
  mutations: Mutations,
  actions: Actions
}

const store = new Vuex.Store({
  modules: {
    index: index
  }
})

export default store


```
#### 运行项目
在bash中输入`npm run dev `


以上工程目录就搭建完毕了，后续components中尽量添加可复用的无状态组件，或者全局状态组件，比如alertbox之类，containers中为页面，至于ajax请求尽量用vuex ，action，去集成，尽量做成函数式编程的风格。这样可以尽可能的保证项目内部代码规整。

项目目录结构以及代码请移步至以下git中查看
[https://github.com/shifeng1993/vue-start](https://github.com/shifeng1993/vue-start "https://github.com/shifeng1993/vue-start")
喜欢这个脚手架的可以 star 和 fork 哦！