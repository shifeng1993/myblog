---
title: 【webpack学习随笔】1.webpack基础以及核心配置
date: 2019-06-08 16:47:00
tags:
  - base
  - webpack
---
webpack我接触是从1.0，之前是概粗略的看了一下，做了个简单的demo，后面再次看是在去年面试的时候，用webpack4.0做面试题。

没有具体详细的写过一个webpack配置策略一直是我自己的弱项，现在webpack更新到了4.0版本。今年打算好好的花几个月补一下这个。

下面是正文

---------------------------------

## webpack是干什么的? - 静态模块打包工具（编译成静态资源）
- 代码转换：TypeScript 编译成 JavaScript、SCSS 编译成 CSS 等。
- 文件优化：压缩 JavaScript、CSS、HTML 代码，压缩合并图片等。
- 代码分割：提取多个页面的公共代码、提取首屏不需要执行部分的代码让其异步加载。
- 模块合并：在采用模块化的项目里会有很多个模块和文件，需要构建功能把模块分类合并成一个文件。
- 自动刷新：监听本地源代码的变化，自动重新构建、刷新浏览器。
- 代码校验：在代码被提交到仓库前需要校验代码是否符合规范，以及单元测试是否通过。
- 自动发布：更新完代码后，自动构建出线上发布代码并传输给发布系统。

## webpack4 需要安装哪两个包
- webpack
- webpack-cli

本地开发环境安装 npm:`--save-dev` yarn:`--dev`
```bash
$ npm install webpack webpack-cli --save-dev
$ yarn add webpack webpack-cli --dev
```

## 当前项目怎么运行webpack打包
`package.json`内将命令加入script
```json
"scripts": {
  "build": "webpack"
},
```

## webpack怎么实现打包（原理）
1. 找到入口，例如`src/index.js`
2. 找到`index.js`引入了哪些模块，并会加载对应的模块 进行抽象语法树（ast）解析
3. index是入口 执行的时候，默认会从入口进行解析 `require` 方法改写成 `__webpack_require__`
(需要找到入口，并找到入口中的所有依赖，之后加载依赖) + 模板 = 渲染之后的结果

## webpack配置文件 `webpack.config.js`
一般不会只是使用cli行进行打包，还需要根据项目具体情况进行具体配置，这时要在项目根目录下新增一个`webpack.config.js`这个文件是基于nodejs来写的，所以使用的是commonjs规范

以上是默认的配置文件名，还可以自定义文件名，通过cli命令`--config xxxxx.js`来指定对应的文件和路径，例如：

我在根目录下放一个`config`文件夹， 我要把webpack配置放在里面，我可以在package.json这么写，这样，使用的就是`config/webpack.base.conf.js` 的配置
```json
"scripts": {
  "build": "webpack --config config/webpack.base.conf.js"  // npm run build 命令
},
```
---------------------------------
## webpack四大核心概念
有了配置文件，就可以在配置文件内写对应的配置，配置主要包含四大核心：
- 入口(entry)
- 输出(output)
- 加载器(loader)
- 插件(plugins)

```js
module.exports = {
  entry: './src/index.js',                    // 配置入口文件的地址
  output: {
    path: path.resolve(__dirname, 'dist'),    // 配置出口文件的目录
    filename: 'bundle.js'                     // 配置出口文件的名称
  },
  module: {},                                 // 配置模块,主要用来配置不同文件的加载器
  plugins: [],                                // 配置插件
  devServer: {}                               // 配置开发服务器
}
```
## 入口(entry)
入口，则是入口文件，分为单入口和多入口两种：
https://www.webpackjs.com/configuration/entry-context/

### 单入口写法
```js
module.exports = {
  entry: './src/index.js',
  mode: 'production'
}
```
### 多入口写法
```js
module.exports = {
  mode: 'production',
  entry: {
    index: './src/index.js',
    client: './src/client.js'
  },                    // 配置入口文件的地址
  output: {
    path: path.resolve(__dirname, 'dist'),    // 配置出口文件的目录
    filename: '[name].js'                     // 配置出口文件的名称
  }
}
```

## 输出(output)
https://www.webpackjs.com/configuration/output/

### 单出口写法
```js
module.exports = {
  entry: './src/index.js',
  output: {
    path: path.resolve(__dirname, 'dist'),    // 配置出口文件的目录
    filename: 'bundle.js'                     // 配置出口文件的名称
  }
  mode: 'production'
}
```
### 多出口写法
filename等等属性都可以根据文档自行配置
```js
module.exports = {
  mode: 'production',
  entry: {
    index: './src/index.js',
    client: './src/client.js'
  },                    // 配置入口文件的地址
  output: {
    path: path.resolve(__dirname, 'dist'),    // 配置出口文件的目录
    filename: '[name].js'                     // 配置出口文件的名称
  }
}
```

## 加载器(loader)
module下的rules 内loader执行顺序，从右到左，从下到上

loader执行顺序（rules内enforce属性）：
- 前置loader （pre）
- 普通loader
- inline-loader（代码内使用）
- 后置loader （post）

## css的loader
### css-loader
解析css语法

开启css模块化
- 在options添加`modules:true`

```js
  module: {
    rules: [
      {
        test: /\.css$/,
        use: ['style-loader']
      },
      {
        test: /\.css$/,
        use: {
          loader: 'css-loader',
          options: {
            modules: true
          }
        }
      }
    ]
  },
```
- css文件内如果有使用预处理的文件，使用之前的loader进行预处理
默认0，设置数字几个就是根据执行顺序往前走几个

```js
  module: {
    rules: [
      {
        test: /\.css$/,
        use: [
          {
            loader: 'style-loader',
            options: {
              insertAt: 'top',
            },
          },
          { // @语法引入了css文件这个文件可能是sass
            loader: 'css-loader',
            options: {
              importLoaders: 1,
              modules: true
            },
          },
          'sass-loader',
        ],
      },
    ]
  },
```

### style-loader
把样式插入到到html中

### node-sass sass-loader
安装sass支持

### less-loader
less支持
### stylus-loader
stylus支持

## js的loader
### babel-loader
调用@babel/core 插件，然后再调用@babel/preset-env进行转换
如果使用了一些新的api 就需要使用@babel/core进行转化

1. 先配置loader

```js
{
  test: /\.js$/,
  use: 'babel-loader'
},
```

2. 配置.babelrc

```json
{
  "presets": [
    ["@babel/preset-env", {
      "useBuiltIns": "usage", // 如果使用了某些api就注入进来，进行转化
      "corejs": 2             // 需要的corejs
    }]
  ],
  "plugins": []
}
```
以上方法 实现普通的代码是没有问题的，但是如果是使用 promise 或者 set map等新的api会放到global对象上。所以会使用以下的库
- @babel/plugin-transform-runtime
依赖于 @babel/runtime，会创建一个沙箱，不会污染全局变量。需要再添加一个@babel/runtime-corejs2

.babelrc
```json
{
  "presets": [
    ["@babel/preset-env"]
  ],
  "plugins": [
    [
      "@babel/plugin-transform-runtime",
      {
        "corejs": 2
      }
    ]
  ]
}
```

### eslint-loader
在babel编译之前进行代码校验，然后运行`npx eslint --init`
```js
{
  test: /\.js$/,
  use: 'eslint-loader'
},
```
### ts-loader
用ts 需要安装ts-loader和typescript
```js
{
  test: /\.ts$/,
  use: 'ts-loader'
},
```
### tslint-loader
在babel编译之前进行代码校验，然后运行`npx tslint --init`
```js
{
  test: /\.ts$/,
  use: 'tslint-loader'
},
```
## 文件的loader
### file-loader
复制文件到打包目录下

### url-loader
如果图片小，可以做成对应的base64，减少浏览器请求次数,如果超过limit大小，仍然是复制文件到打包目录下

## 插件(plugins)
基础插件其实就那么几个，其余的都是根据项目实际情况，自选一些优化插件
### html-webpack-plugin 
用来根据一个模板生成打包后的html
```js
  const path = require('path');
+ const HtmlWebpackPlugin = require('html-webpack-plugin');

  module.exports = {
    entry: {
      app: './src/index.js',
      print: './src/print.js'
    },
+   plugins: [
+     new HtmlWebpackPlugin({
+       title: 'Output Management'
+     })
+   ],
    output: {
      filename: '[name].bundle.js',
      path: path.resolve(__dirname, 'dist')
    }
  };
```
### clean-webpack-plugin
清空打包文件的插件
```js
  const path = require('path');
  const HtmlWebpackPlugin = require('html-webpack-plugin');
+ const { CleanWebpackPlugin } = require('clean-webpack-plugin');

  module.exports = {
    entry: {
      app: './src/index.js',
      print: './src/print.js'
    },
    plugins: [
+     new CleanWebpackPlugin(),
      new HtmlWebpackPlugin({
        title: 'Output Management'
      })
    ],
    output: {
      filename: '[name].bundle.js',
      path: path.resolve(__dirname, 'dist')
    }
  };
```

### mini-css-extract-plugin 
从js分离出css
### optimize-css-assets-webpack-plugin
压缩css

## devServer 开发服务器
可以配置一个本地的开发环境，可以配置反向代理等作用。
```js
devServer: {
    port: 8080,                               // 端口号
    open: true,                               // 自动打开浏览器
    compress: true,                           // 启用gzip压缩
    proxy: {
      '/api': {
        target: 'http://localhost:3000',
        changeOrigin: true                    // 改变主机的host
      }
    }
  }
```

## webpack多页面打包配置示例
```js
  const path = require('path');
  const HtmlWebpackPlugin = require('html-webpack-plugin');
  
  // 入口配置
  const entry = {
    index: './src/index.js',
    client: './src/client.js'
  };

  // 根据入口文件自动生成htmlplugins
  let htmlPlugins = Object.keys(entry).map((chunkName) => {
    return new HtmlWebpackPlugin({
      filename: `${chunkName}.html`,
      chunks: [chunkName]
    })
  })

  module.exports = {
    mode: 'production',
    entry: entry,
    output: {
      path: path.resolve(__dirname, 'dist'),    // 配置出口文件的目录
      filename: '[name].js'                     // 配置出口文件的名称
    },
    module: {},                                 // 配置模块,主要用来配置不同文件的加载器
    plugins: [
      ...htmlPlugins                            // 展开htmlplugins
    ],                                          // 配置插件
    devServer: {}                               // 配置开发服务器
  }
```
## webpack怎么区分是生产环境还是开发环境
在配置内加入`mode: 'production'` 或者在cli参数内加入 `webpack --mode=production`
```js
module.exports = {
  entry: './src/index.js',
  mode: 'production'
}
```
- `development`会将 `process.env.NODE_ENV` 的值设为 `development`。启用 `NamedChunksPlugin` 和 `NamedModulesPlugin`

- `production`会将 `process.env.NODE_ENV` 的值设为 `production`。启用 `FlagDependencyUsagePlugin`, `FlagIncludedChunksPlugin`, `ModuleConcatenationPlugin`, `NoEmitOnErrorsPlugin`, `OccurrenceOrderPlugin`, `SideEffectsFlagPlugin` 和 `UglifyJsPlugin`.



