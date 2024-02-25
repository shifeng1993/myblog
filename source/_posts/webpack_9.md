---
title: 【webpack学习随笔】7.webpack配置javascript兼容性
date: 2020-12-06 13:52:00
tags:
  - base
  - webpack
---
## babel
- babel是一个javascript编译器，主要将es6+版本的代码转换为向下兼容js代码，方便js运行在当前和旧的浏览器中
- 功能：语法转换

## 安装
```bash
npm install @babel/core @babel/cli -D
```

## 命令和使用

### src/index.js
```js
const sum = (a, b)=> a + b;
console.log(sum(1, 2));
```

### package.json
```json
{
  "scripts": {
    "bulld": "babel src --out-dir dist --watch"
  } 
}
```

## 插件
- babel是一个编译器 （输入源码=> 输出编译后的代码），过程分为三个阶段：
  - 解析
  - 转换
  - 打印输出

- babel是开箱即用的，但是什么动作都不做，const buildCode = (code)=> code，将代码解析后再输出同样的代码，想要babel做一些实际的工作，就需要一些插件
  
### 安装插件
```bash
npm install @babel/plugin-transform-arrow-functions -D
```

### .babelrc
```json
{
  "plugins": ["@babel/plugin-transform-arrow-functions"]
}
```

### 预设
- @babel/preset-env 插件，可以使用最新的js语法，而不需要去管理语法转换器，可选支持目标浏览器的polyfills

```js
module.exports = function(){
  return {
    plugins: ["pluginA", "pluginB", "pluginC"]
  }
}
```
#### 安装
```bash
npm install @babel/preset-env -D
```

#### .babelrc
```json
{
  "plugins": ["@babel/preset-env"]
}
```

#### browsers
- @babel/preset-env 会根据配置生成目标环境，生成插件列表来编译
- 可以使用 .browserslistrc 文件来指定环境

```js
> 0.25% // 市场份额大于0.25%
last 2 Chrome Versions // 最近两个chrome版本
```

### polyfill
- @babel/preset-env 插件只转换新的语法，不转换新的api，比如 Iterator，Generator，Set，Maps，Proxy，Reflect，Symbol，Promise 等全局对象，以及一些全局对象上的方法，例如Object.assgin都不会转换
- 官方给出 @babel/polyfill 和 @babel/runtime 两种解决方案，来解决这种，全局对象或者全局对象上方法不足的情况
- @babel/runtime 更适合在组件库中使用，而 @babel/polyfill 适合在业务中使用
  
#### @babel/polyfill
- 可以完整模拟es2015+的环境
- 可以使用Promise,WeekMap等新对象，Array.from，Object.assgin等静态方法，Array.prototype.includes等实例方法
- 原理是通过向全局对象和内置对象的prototype上添加方法来实现的，缺点是会造成全局空间污染
- V7.4.0版本已废弃

安装
```bash
npm install @babel/polyfill core-js@3 --save
npm install webpack webpack-cli babel-loader -D
```

- useBuiltIns: false 此时不对polyfill做操作
- useBuiltIns: 'entry' 根据兼容引入浏览器不兼容的 polyfill
  - corejs: 3 配置entry，如果是corejs是3的版本，则 import '@babel/polyfill' 需要改成 import 'corejs/stable'; import "regenerator-runtime/runtime"
  - corejs@2分支已经不会再添加新特性，新增特性都会添加在corejs@3中
- useBuiltIns: 'usage' 会根据兼容性，以及代码中用到的api进行polyfill，实现了按需添加
```js
module.export = {
  module:{
    rules: [
      {
        test: /\.js?$/,
        use: {
          loader: 'babel-loader',
          options: {
            persets: [
              ["@babel/preset-env", {
                useBuiltIns: false
              }]
            ]
          }
        }
      }
    ]
  }
}
```

#### babel-runtime
- babel为了解决全局变量污染的问题，提供了单独的babel-runtime用来提供编译模块的工机函数
- 更像是一种按需加载的实现，例如需要使用promise，只需要在头部引入 import Promise from 'babel-runtime/core-js/promise'

1. 安装
```bash
npm install babel-runtime -D
```

2. src/indexjs
```js
import Promise from 'babel-runtime/core-js/promise';
const p = new Promise((resolve)=>{
  resolve('ok');
})

console.log(p)
```

#### #### babel-plugin-transform-runtime
- 可以自动按需引入babel-runtime 下的工具函数。
- 除了可以减少编译后提及之外，还能为代码创建一个沙盒环境

1. 安装
```bash
npm install @babel/plugin-transform-runtime -D
```

2. webpack.config.js
```js
 module: {
    rules: [
      {
        test: /\.js$/,
        include: path.resolve(__dirname, 'src'),
        exclude: /node_modules/,
        use: [
          {
            loader: 'babel-loader',
            options: {
              presets: [
                ['@babel/perset-env']
              ],
              plugins: [
                [
                  '@babel/plugin-transform-runtime', {
                    corejs: 3, // 如果遇到了Promise，会自动引入polyfill
                    helpers:true, // 移除内联的babel-helpers，并自动引入babel-runtime/helpers来替代
                    regenerator:true // 是否开启gen函数转换成使用regenerator runtime 来避免污染全局作用域
                  }
                ]
              ]
            }
          }
        ]
      },
```
### 执行顺序
- 插件(plugins)先执行，预设(presets)后执行
- 插件顺序从前往后
- 预设顺序从后往前

## 上一节
- [运行速度优化](/2020/12/06/webpack_8/)
