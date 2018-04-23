---
title: 【vue2.0实战分享】创建一个包含vue-router和vue组件的webapp
categories: vue
date: 2016-10-15
tags:
  - vue
  - 前端
---
### 添加Bootstrap和Sass到项目中

为了美化demo界面引入Bootstrap，将bootstrap在此路径`vue-demo1/index.html`文件中用link引入即可:

`<link rel="stylesheet" href="/static/css/bootstrap.min.css">`

项目内sass预编译的使用
在`.vue`组件内部的 `style`标签内添加`lang="scss"`属性即可 ，
`scoped`属性是组件独有的意思，

至此就添加完毕了

### 文件解释
`/src/main.js`作为页面的主入口文件，接入整个webapp的router和vuex全局状态管理以及根组件app.vue，以及引入一些全局的插件和组件。

`/src/App.vue`作为组件的主入口组件，负责包括页面和复用组件之间的总调用，与router-view一起负责页面的路由切换以及灵活展现

### 创建页面
在`/src/components/`内创建一个`navbar.vue`来作为项目的可复用组件。并添加如下代码在`template标签`内
```html
  <nav class="navbar navbar-inverse">
    <div class="container-fluid">
      <div class="navbar-header">
        <button type="button" class="navbar-toggle collapsed" data-toggle="collapse" data-target="#main-navbar">
          <span class="sr-only">Toggle navigation</span>
          <span class="icon-bar"></span>
          <span class="icon-bar"></span>
          <span class="icon-bar"></span>
        </button>
        <router-link to="/" class="navbar-brand">Index</router-link>
      </div>
      <div class="collapse navbar-collapse" id="main-navbar">
        <ul class="nav navbar-nav">
          <li><router-link to="/home">Home</router-link></li>
          <li><router-link to="/about">About</router-link></li>
        </ul>
      </div><!-- /.navbar-collapse -->
    </div><!-- /.container-fluid -->
  </nav>
```
在`/src/containers/`内创建一个`index.vue`来作为项目的主页面，
并添加如下代码在`template标签`内
```html
 <div id="container">
    <NavBar></NavBar>
    <router-view></router-view>
  </div>
```
在`script标签`内用`import NavBar from '../components/NavBar'`引入组件，
并在`components`属性内加载组件，如下代码：
```javascript
import NavBar from '../components/NavBar'
  export default {
    name: 'Index',
    data () {
      return {
      }
    },
    components: {
      NavBar
    }
  }
```
在`/src/containers/`内创建一个`home.vue`来作为项目的子页面。
并添加如下代码在`template标签`内
```html
 <h1>home</h1>
```

在`/src/containers/`内创建一个`about.vue`来作为项目的子页面。
并添加如下代码在`template标签`内
```html
 <h1>about</h1>
```
至此，页面就创建完毕了。

### 创建路由
在`/src/routes/index.js`文件内`routes`添加一条路由记录,
这个记录是一个数组，数组内部包含路由记录对象，这里记住，子路由children是通过组件内部的router-view去嵌套渲染的
```javascript
const routes = [
  {
    path: '/',
    component: Index,
    children: [
      {
        path: 'home',
        component: Home
      },
      {
        path: 'about',
        component: About
      }
    ]
  }
]
```
至此，路由就创建完毕了。

接下来可以运行`npm run dev` 来查看demo


注：本demo使用的脚手架为：[https://github.com/shifeng1993/vue-start](https://github.com/shifeng1993/vue-start "https://github.com/shifeng1993/vue-start")

demo源码：[https://github.com/shifeng1993/vue-demo1](https://github.com/shifeng1993/vue-demo1 "https://github.com/shifeng1993/vue-demo1")
