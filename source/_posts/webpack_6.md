---
title: 【webpack学习随笔】4.编译时间优化
date: 2020-12-06 13:24:00
tags:
  - base
  - webpack
---
> 编译时间优化思路有两种
> - 缩小查找范围
> - 减少要处理的文件

## 缩小查找范围

### extensions
- 指定 extensions 后不用在 import 和 require 的时候加扩展名
- 查找的时候回一次尝试添加扩展名进行匹配

```js
  resolve: {
+    extensions: ['.js', '.jsx', '.json']
  },
```
### alias 
- 配置别名可以加快webpack查找模块的速度

例如：需要配置一个公共的utils类
```js
  resolve: {
    ...
+    alias: {
+      utils: path.join(__dirname, './src/utils/index.js')
+    }
  },
```

### modules
- 对于直接声明依赖名的模块，webpack会使用类似nodejs一样的路径搜索，搜索node_modules目录
- 对于可以确定项目内所有的第三方依赖模块都是在根目录下的node_modules 中的话可以直接指定默认配置

例如内网需要加载antdv的组件库依赖，不想在package.json安装，直接可以用modules进行加载依赖
```js
  resolve: {
    ...
+    modules: ['node_modules', 'antdvDeps']
  },
```

### mainFields
- 默认情况下package.json文件，按照main字段的文件路径来查找文件
```js
  resolve: {
    ...
    mainFields: ['main', 'module']
  },
```

### mainFiles
- 当目录没有package.json时候，默认使用index文件
```js
  resolve: {
    ...
    mainFiles: ['index', 'main']
  },
```

### oneOf
- 每个文件对于rules中所有的规则都会遍历一遍，如果使用oneOf就可以，只匹配一个即可退出
- 在oneOf中不能两个配置处理同一种类型文件

oneOf 只可能匹配数组中的某一个，找到一个就不会再继续查找剩下的loader
```js
  module: {
    rules: [
      {
        oneOf: [
          {
            test: /\.css$/,
            use: [
              'style-loader',
              'css-loader'
            ]
          },
          {
            test: /\.less$/,
            use: [
              'style-loader',
              'css-loader',
              'less-loader'
            ]
          }
        ]
      }
    ]
  },
```

### externals(外部变量+cdn)
- 如果想引用一个库，又不想让webpack打包进bundle，并且不影响我们在程序中用cmd amd或者window/global等全局方式进行使用
#### 安装
```bash
npm install jquery html-webpack-externals-plugin -D
```

#### webpack.config.js
```js
  externals: {
    jquery: 'jQuery'
  },
  plugins: [
    ...
    new HtmlWebpackExternalsPlugin({
      externals: [{
        module: 'jquery',   // 模块名
        entry: 'https://cdn.bootcdn.net/ajax/libs/jquery/3.5.1/jquery.min.js',
        global: 'jQuery'    // 全局对象名
      }]
    }),
  ]
```

#### 使用jquery
src/index.js
```js
import $ from 'jQuery';
console.log($)
```

### resolveLoader
- 属性和resolve一样，与resolve不一样的是，resolve只对普通模块有用，resolveLoader是只对loader有用

例如：自定义一个loader
#### loaders/logger-loader.js
```js
module.exports = function (source) { // loader是一个function
  console.log('logger')
  return source
}
```
#### webpack.config.js
```js
const path = require('path');
const loadersPath = path.resolve(__dirname, 'loaders');

module.exports = smw.wrap({
  ...
  resolveLoader: {
    modules: [loadersPath, 'node_modules'] // 所有的loader默认查找的目录，需要加上node_modules目录
  },
  module: {
    rules: [
      {
        oneOf: [
          {
            test: /\.css$/,
            use: [
              'logger-loader',
              'style-loader',
              'css-loader'
            ]
          },
          ...
        ]
      }
    ]
  },
  ...
})
```

## noParse
- module.noParse字段，可以用于配置哪些模块文件的内容不需要进行解析
- 不需要解析依赖的第三方大型类库等可以用这个字段来配置，提高整体构建速度(jquery,lodash等)
- 使用noParse进行忽略的模块文件中不能使用 import、require等语法
  
### src/index.js
```js
let title = require('./title');
console.log(title)
```
### webpack.config.js
```js
module.exports = smw.wrap({
  module: {
+   noParse: /title.js/     // 正则表达式
  }
})
```

## IgnorePlugin
- ignore-plugin 用于忽略某些特定的模块，让webpack不把这些指定的模块打包进去
- requestRegExp 匹配（test）资源请求路径的正则表达式
- contestRegExp （可选）匹配（test）资源上下文（目录）的正则表达式
  
例如：moment会将所有语言包和核心功能一起打包，可以使用ignoreplugin忽略掉所有的语言包，然后在按需引入

### 安装moment
```bash
npm install moment -S
```

### src/index.js
```js
import moment from 'moment';
import 'moment/locale/zh-cn';   // 单另引入需要的语言包
```

### weback.config.js
```js
const webpack = require('webpack');
module.exports = smw.wrap({
  ...
  plugins: [
    ...
    new webpack.IgnorePlugin({
      resourceRegExp: /^\.\/locale$/, // 资源正则
      contextRegExp: /moment$/    // 上下文目录
    }),
  ]
})
```

## thread-loader（多进程）
- 把 thread-loader放在其他loader前，这个loader后的loader就会单独在一个worker池中运行
- include 表示哪些目录中的.js 需要进行
- exclude 表示哪些目录中的.js 不需要进行
- exclude的优先级要高于include，尽量避免使用exclude，使用include

### 安装
```bash
npm install thread-loader babel-loader @babel/core @babel/preset-env -D
```

### webpack.config.js
```js
module.exports = smw.wrap({
  module: {
    rules: [
      {
        test: /\.js$/,
        include: path.resolve(__dirname, 'src'),
        exclude: /node_modules/,
        use: [
          {
            loader: 'thread-loader',
            options: {
              workers: 3
            }
          },
          'babel-loader'
        ]
      },
    ]
  }
})
```

## 利用缓存

### babel-loader
```js
module.exports = smw.wrap({
  module: {
    rules: [
      {
        test: /\.js$/,
        include: path.resolve(__dirname, 'src'),
        exclude: /node_modules/,
        use: [
          {
            loader: 'thread-loader',
            options: {
              workers: 3
            }
          },
          {
            loader: 'babel-loader',
            options:{
              cacheDirectory: true  // 启动babel缓存
            }
          }
        ]
      },
    ]
  }
})
```

### cache-loader
在一些性能开销较大的loader前添加，可将结果缓存到磁盘中;
默认保存在 node_modules/.cache/cache-loader 目录下
```bash
npm install cache-loader -D
```

```js
          {
            test: /\.css$/,
            use: [
              'cache-loader',
              'logger-loader',
              'style-loader',
              'css-loader'
            ]
          },
```

### hard-source-webpack-plugin
- 为模块提供中间缓存，默认缓存存放的路径是 node_modules/.cache/hard-source
- 配置 hard-source-webpack-plugin 后，首次构建时间不会有太大变化，但是从第二次开始，构建时间大概可减80%左右
- webpack5已经内置了模块缓存，不需要再使用此插件
  
#### 安装
```bash
npm install hard-source-webpack-plugin -D
```

#### webpack.config.js
```js
const HardSourceWebpackPlugin = require('hard-source-webpack-plugin');

module.exports = smw.wrap({
  ...
  plugins: [
    ...
    new HardSourceWebpackPlugin()
  ]
})
```



## 最终webpack.config.js
```js
const path = require('path');
const notifier = require('node-notifier');
const HtmlWebpackPlugin = require('html-webpack-plugin');
const FriendlyErrorsWebpackPlugin = require('friendly-errors-webpack-plugin');
const SpeedMeasureWebpack5Plugin = require('speed-measure-webpack5-plugin');
const HtmlWebpackExternalsPlugin = require('html-webpack-externals-plugin');
const {BundleAnalyzerPlugin} = require('webpack-bundle-analyzer');
const loadersPath = path.resolve(__dirname, 'loaders')
const smw = new SpeedMeasureWebpack5Plugin();
const webpack = require('webpack');

module.exports = smw.wrap({
  mode: 'development',      // 配置模式
  context: process.cwd(),   // 上下文目录 根目录
  devtool: "source-map",    // 调试工具
  entry: {
    main: './src/index.js'
  },
  output: {
    path: path.resolve(__dirname, 'dist'),  // 输出的路径
    filename: '[name].js'                   // 输出的文件名
  },
  resolve: {
    extensions: ['.js', '.jsx', '.json'],
    alias: {
      utils: path.join(__dirname, './src/utils/index.js')
    }
  },
  resolveLoader: {
    modules: [loadersPath, 'node_modules']
  },
  externals: {
    jquery: 'jQuery'
  },
  module: {
    rules: [
      {
        test: /\.js$/,
        include: path.resolve(__dirname, 'src'),
        exclude: /node_modules/,
        use: [
          {
            loader: 'thread-loader',
            options: {
              workers: 3
            }
          },
          'babel-loader'
        ]
      },
      {
        oneOf: [
          {
            test: /\.css$/,
            use: [
              'cache-loader',
              'logger-loader',
              'style-loader',
              'css-loader'
            ]
          },
          {
            test: /\.less$/,
            use: [
              'style-loader',
              'css-loader',
              'less-loader'
            ]
          }
        ]
      }
    ]
  },
  plugins: [
    new HtmlWebpackPlugin({
      template: './src/index.html'
    }),
    new HtmlWebpackExternalsPlugin({
      externals: [{
        module: 'jquery',   // 模块名
        entry: 'https://cdn.bootcdn.net/ajax/libs/jquery/3.5.1/jquery.min.js',
        global: 'jQuery'    // 全局对象名
      }]
    }),
    new FriendlyErrorsWebpackPlugin({
      onErrors: (severity, errors) => {
        const error = errors[0];
        notifier.notify({
          title: severity + ': ' + error.name,
          message: error.message
        })
      }
    }),
    new webpack.IgnorePlugin({
      resourceRegExp: /^\.\/locale$/, // 资源正则
      contextRegExp: /moment$/    // 上下文目录
    }),
    new BundleAnalyzerPlugin({
      analyzerMode: 'disabled',   // 默认不启动8888服务
      generateStatsFile: true,    // 生成stats.json文件
    })
  ]
})
```
## 上一节
- [如何进行数据和性能分析](/2020/12/05/webpack_5/)

## 下一节
- [编译体积优化](/2020/12/06/webpack_7/)
