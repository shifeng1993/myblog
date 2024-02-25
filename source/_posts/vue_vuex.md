---
title: 【vue2.0实战分享】如何在项目中引入和使用vuex
date: 2016-10-23
categories: vue
tags:
  - vue
  - 前端
---

### 引入vuex

因为vuex是加载在vue根实例上的，所以先在`app.vue`引入`store`，并在vue根实例中引入
代码如下
```javascript
import Vue from 'vue'
import App from './App'
import router from './routes'
// 引入vuex创建的store实例，路径 './stores' 为 './stores/index.js'的简写
import store from './stores'

// 创建vue实例并渲染到app
new Vue({
  router, // 为router: router的简写
  store, // store: store的简写
  render: h => h(App)
}).$mount('#app')

```

store是vuex创建的一个实例，用来实现全局状态的管理，包括组件之间状态管理以及全局组件的状态分发等作用。创建以下路径文件`/src/stores/index.js`
并添加如下代码


```javascript
// 引入依赖
import Vue from 'vue'
import Vuex from 'vuex'

// 使用vuex
Vue.use(Vuex)

// 引入state模块
import states from './states'
import mutations from './mutations'
import actions from './actions'

/*
 * 第一种 state模块化
 * 适合大型项目以及状态多的项目写法
 * 注：模块化状态只是模块化state不分，其余getters，mutations，actions，是不分模块的，注意不要重命名
*/

// vuex 模块
// const index = {
//   state: states,
//   mutations: mutations,
//   actions: actions
// }
//
// const store = new Vuex.Store({
//   modules: {
//     index: index
//   }
// })

/*
 * 第二种 不分模块状态共享
 * 适合中小型项目以及状态少的项目写法
 */

const store = new Vuex.Store({
  state: states,
  mutations: mutations,
  actions: actions
})

export default store

```

### 搭建目录
创建`/src/stores/states/index.js`添加如下代码
```javascript
// states
const states = {
}

export default states

```
创建`/src/stores/mutations/index.js`添加如下代码
```javascript
// mutations
const mutations = {
}

export default mutations

```
创建`/src/stores/actions/index.js`添加如下代码
```javascript
// actions
const actions = {
}

export default actions

```

到此vuex的引入和目录搭建已经完了

### vuex实践小demo

本demo是一个用全局状态驱动的loading组件例子，旨在掌握vuex的基本用法。

vuex中文文档
https://vuex.vuejs.org/zh-cn/

### 创建loading组件

创建`/src/components/Loading.vue`添加如下代码

这里的computed计算属性，取值为store 内的全局状态，isLoading为布尔值，
真，则使用v-if来显示组件，假，则为不显示。
```javascript
<template>
  <div class="loading_jump" v-if="isLoading">
    <div class="loading_jump1"></div>
    <div class="loading_jump2"></div>
  </div>
</template>
<script>
  export default {
    name: 'Loading',
    data () {
      return {
        msg: 'Hello Vue!'
      }
    },
    computed: {
      isLoading () {
        return this.$store.state.isLoading
      }
    },
    mounted () {
    },
    methods: {},
    components: {}
  }
</script>

<style lang="scss" rel="stylesheet/scss" scoped>
  .loading_jump {
    width: 1rem;
    height: 1rem;
    position: fixed;
    top: 50%;
    left: 50%;
    margin-top: -.5rem;
    margin-left: -.5rem;
    z-index: 9999;
    .loading_jump1 {
      width: 1rem;
      height: 1rem;
      position: absolute;
      background-image: url(../assets/loading.png);
      background-size: 100% auto;
      background-position: 0 -1rem;
      -webkit-animation: loadanimate 3.6s steps(6) infinite, updown .8s infinite;
      animation: loadanimate 3.6s steps(6) infinite, updown .8s infinite;
    }

    .loading_jump2 {
      width: 1rem;
      height: .7rem;
      position: absolute;
      bottom: -0.3rem;
      background: url('data:image/svg+xml;base64,PHN2ZyB4bWxucz0iaHR0cDovL3d3dy53My5vcmcvMjAwMC9zdmciIHZpZXdCb3g9IjAgMCAxMDAgMTAwIj48ZWxsaXBzZSBjeD0iNDgiIGN5PSIxMi40NjMiIGZpbGw9IiNEM0U2RkYiIGZpbGwtcnVsZT0iZXZlbm9kZCIgcng9IjQ3Ljg5IiByeT0iMTIuMzY5IiB0cmFuc2Zvcm09InRyYW5zbGF0ZSgyIDM4KSIvPjwvc3ZnPg==') no-repeat;
      background-size: 100% auto;
      -webkit-animation: updownbigsmall .8s infinite;
      animation: updownbigsmall .8s infinite;
    }
  }

  @-webkit-keyframes loadanimate {
    0% {
      background-position: 0 0rem;
    }
    100% {
      background-position: 0 -6rem;
    }
  }

  @keyframes loadanimate {
    0% {
      background-position: 0 0rem;
    }
    100% {
      background-position: 0 -6rem;
    }
  }

  @-webkit-keyframes updown {
    0% {
      -webkit-transform: translateY(0rem);
    }
    50% {
      -webkit-transform: translateY(-1rem);
    }
    100% {
      -webkit-transform: translateY(0rem);
    }
  }

  @keyframes updown {
    0% {
      transform: translateY(0rem);
    }
    50% {
      transform: translateY(-1rem);
    }
    100% {
      transform: translateY(0rem);
    }
  }

  @-webkit-keyframes updownbigsmall {
    0% {
      -webkit-transform: translateY(0rem) scale(1);
    }
    50% {
      -webkit-transform: translateY(.2rem) scale(.4);
    }
    100% {
      -webkit-transform: translateY(0rem) scale(1);
    }
  }

  @keyframes updownbigsmall {
    0% {
      transform: translateY(0rem) scale(1);
    }
    50% {
      transform: translateY(.2rem) scale(.4);
    }
    100% {
      transform: translateY(0rem) scale(1);
    }
  }
</style>


```
### 把组件状态变为全局
- 注册初始状态

在`/src/stores/states/index.js`内添加一个` isLoading: false`
代码如下
```javascript
// states
const states = {
  // 是否在加载中
  isLoading: false
}

export default states

```
- 添加同步函数

在`/src/stores/mutations/index.js`内添加同步函数`SET_LOADING`
代码如下
```javascript
// mutations
const mutations = {
  // 设置是否在加载
  SET_LOADING (state, platform) {
    state.isLoading = platform
  }
}

export default mutations

```

- 添加异步函数

在`/src/stores/actions/index.js`内添加异步函数`setLoading`
代码如下
```javascript
// actions
const actions = {
  setLoading ({commit}, platform) {
    commit('SET_LOADING', platform)
  }
}

export default actions


```

接下来全局组件就配置好了。不管哪一个组件给action内的setLoading函数发dispatch，都会有全局状态的变化，是不是很简单呢

### 触发组件
loading组件加载的话是全局都有，一般加在根组件内，`mounted`和`beforeUpdate`，分别是app在渲染，和进行router切换的周期，所以我们用这两个生命周期进行触发。

在`/src/app.vue` 内加入如下代码

```javascript
<template>
  <div id="app">
    <Loading></Loading>
    <transition name="fade" mode="out-in" v-if="showMe">
      <router-view></router-view>
    </transition>
  </div>
</template>

<script>
  import Loading from './components/Loading'
  export default {
    name: 'app',
    data () {
      return {
        showMe: false // 是否展示当前页面
      }
    },
    components: {
      Loading
    },
    computed: {},
    mounted () {
      this.loadingtime(1500)
    },
    beforeUpdate () {
      this.loadingtime(300)
    },
    methods: {
      loadingtime (time) {
        // 设置当前状态为加载中
        this.$store.dispatch('setLoading', true)
        // 模拟请求等待
        setTimeout(() => {
          // 页面显示
          this.showMe = true
          this.$store.dispatch('setLoading', false)
        }, time)
      }
    }
  }
</script>

<style lang="scss">
  #app {
    overflow-x: hidden;
    overflow-y: auto;
  }

  #app,
  body,
  html {
    height: 100%;
    width: 100%;
    overflow: hidden;
  }

  .fade-enter-active, .fade-leave-active {
    transition: opacity .3s;
  }

  .fade-enter, .fade-leave-active {
    opacity: 0;
  }
</style>

```

加入后保存，刷新就可以看到被调用的loading组件了。


注：本demo使用的脚手架为：[https://github.com/shifeng1993/vue-start](https://github.com/shifeng1993/vue-start "https://github.com/shifeng1993/vue-start")

demo源码：[https://github.com/shifeng1993/vue-demo2](https://github.com/shifeng1993/vue-demo2 "https://github.com/shifeng1993/vue-demo2")



