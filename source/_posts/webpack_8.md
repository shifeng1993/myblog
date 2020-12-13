---
title: 【webpack学习随笔】6.运行速度优化
date: 2020-12-06 16:30:00
tags:
  - base
  - webpack
---
## 代码分割
- 对于大型web应用来说，将所有的代码都放在一个文件中是不够有效的。特别是某些代码块是在某些特殊时候才会被用到
- webpack有个功能就是将代码库分割成chunks代码块，当代码运行到需要他们的时候再加载
  

### 入口点分割
entry中配置 多页应用（MPA）

- 问题：
1. 如果入口chunks之间包含重复代码块，那重复代码块都会被引入到各个bundle中
2. 不够灵活，并且不能将核心应用程序逻辑，进行动态拆分

```js
entry:{
  login:'./src/login.js',
  main: './src/main.js'
}
```

### 懒加载
当用户当前需要什么功能就只加载这个功能对应的代码（按需加载）

- vue，angular，react 的懒加载组件都是动态import

- 一般采用以下原则
1. 对于功能进行划分，每一类一个chunk
2. 首次打开页面需要的功能直接加载，尽快展示给用户，某些依赖大量代码的功能点可以按需加载
3. 被分割出去的代码需要一个按需加载的时机

```js
()=> import('./xxx.js')
```

### 预加载 (preload)
- 使用预加载，表示该模块肯定会被用到，需要提前进行获取
- <link rel="preload" as="script" herf="xxx.js"></link>

```js
document.querySelector('#btn').addEventListener('click',()=>{
  import(/* webpackChunkName: 'hello',webpackPreload: true */'./hello').then((res)=>{
    console.log(res.default)
  })
})

```

### 预获取 (prefetch)
- 使用预获取，表示该模块可能后面会用到，浏览器会在空闲时间下载该模块
- <link rel="prefetch" as="script" herf="xxx.js"></link>

```js
document.querySelector('#btn').addEventListener('click',()=>{
  import(/* webpackChunkName: 'hello',webpackPrefetch: true */'./hello').then((res)=>{
    console.log(res.default)
  })
})

```

扩展：
- preload 预加载 资源肯定会用到，优先级较高，需要提前获取，会影响或者阻塞页面中关键资源加载，可能会有性能问题 
- prefetch 预获取 资源有可能用到， 是在浏览器空闲时间加载，没有性能问题

[资源加载优先级](https://juejin.cn/post/6844903789518946311)


### 提取公共代码 splitChunks

- module js的webpack模块化规范，es6，commonjs等
- chunk 分为三种
  - 项目入口
  - import()动态引入
  - splitchunks 拆分出的代码
- bundle 是对chunk打包压缩后的产物

```js
module.exports = {
  //...
  optimization: {
    splitChunks: {
      chunks: 'async', // 默认只分割异步代码块 async(动态加载模块)，initial(入口模块)，all(全部模块入口和动态的)。
      minSize: 20000, // 分割出去代码块的最小体积 0 表示不限制
      maxSize: 0, // 分割出去代码块的最大体积 0 表示不限制
      minRemainingSize: 0, // 分割后剩下体积，0表示不限制，webpack5新增参数
      minChunks: 1, // 模块引用几次后会被分割
      maxAsyncRequests: 30, // 按需加载时最大并行请求数。
      maxInitialRequests: 30, // 入口点的最大并行请求数
      automaticNameDelimiter: '~', // 名称分割符号
      enforceSizeThreshold: 50000, // 强制阈值 webpack5新增参数
      cacheGroups: {  // 缓存组
        defaultVendors: {  // 第三方模块
          test: /[\\/]node_modules[\\/]/, // 正则匹配路径
          priority: -10, // 优先级 ，一个模块同时存在于多个缓存组，看谁优先级高，就分到谁
          reuseExistingChunk: true // 如果当前块包含已从主捆绑包中拆分出的模块，则将重用它，而不是生成新的模块。
        },
        default: { 
          minChunks: 2, // 被几个入口引用过，最少几次提取
          priority: -20, 
          reuseExistingChunk: true // 如果当前块包含已从主捆绑包中拆分出的模块，则将重用它，而不是生成新的模块。
        }
      }
    }
  }
};
```

## 上一节
- [编译体积优化](/2020/12/06/webpack_7/)

## 下一节
- [webpack配置javascript兼容性](/2020/12/06/webpack_9/)