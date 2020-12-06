---
title: 【webpack学习随笔】3.如何进行数据和性能分析
date: 2020-12-05 16:06:00
tags:
  - base
  - webpack
---
## 日志美化
- friendly-errors-webpack-plugin 可以识别某些类型的wepack错误，并清理和聚合优先级
- node-notifier 提供系统通知

### 安装依赖
```bash
npm install friendly-errors-webpack-plugin node-notifier -D
```

### webpack.config.js
```js
const path = require('path');
const HtmlWebpackPlugin = require('html-webpack-plugin');
+ const FriendlyErrorsWebpackPlugin = require('friendly-errors-webpack-plugin');
+ const notifier = require('node-notifier');

module.exports = {
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
  plugins: [
    new HtmlWebpackPlugin(),
+   new FriendlyErrorsWebpackPlugin({
+     onErrors: (severity, errors) => {
+       const error = errors[0];
+       notifier.notify({
+         title: severity + ': ' + error.name,
+         message: error.message
+       })
+     }
+   })
  ]
}
```


## 速度分析
- speed-measure-webpack5-plugin 分析打包速度


### 安装依赖
```bash
npm install speed-measure-webpack5-plugin -D
```

### webpack.config.js
```js
const path = require('path');
const notifier = require('node-notifier');
const HtmlWebpackPlugin = require('html-webpack-plugin');
const FriendlyErrorsWebpackPlugin = require('friendly-errors-webpack-plugin');
+const SpeedMeasureWebpack5Plugin = require('speed-measure-webpack5-plugin');

+const smw = new SpeedMeasureWebpack5Plugin();

+module.exports = smw.wrap({
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
  plugins: [
    new HtmlWebpackPlugin(),
    new FriendlyErrorsWebpackPlugin({
      onErrors: (severity, errors) => {
        const error = errors[0];
        notifier.notify({
          title: severity + ': ' + error.name,
          message: error.message
        })
      }
    })
  ]
+})
```

## 文件体积监控
- webpack-bundle-analyzer 生成代码分析报告，包括文件包含，大小占比，模块关系，依赖项，文件是否重复，压缩后大小等

### 安装依赖
```bash
npm install webpack-bundle-analyzer -D
```

### webpack.config.js
```js
const path = require('path');
const notifier = require('node-notifier');
const HtmlWebpackPlugin = require('html-webpack-plugin');
const FriendlyErrorsWebpackPlugin = require('friendly-errors-webpack-plugin');
const SpeedMeasureWebpack5Plugin = require('speed-measure-webpack5-plugin');
+const {BundleAnalyzerPlugin} = require('webpack-bundle-analyzer');

const smw = new SpeedMeasureWebpack5Plugin();

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
  plugins: [
    new HtmlWebpackPlugin(),
    new FriendlyErrorsWebpackPlugin({
      onErrors: (severity, errors) => {
        const error = errors[0];
        notifier.notify({
          title: severity + ': ' + error.name,
          message: error.message
        })
      }
    }),
+   new BundleAnalyzerPlugin({
+     analyzerMode: 'disabled',   // 默认不启动8888服务
+     generateStatsFile: true,    // 生成stats.json文件
+   })
  ]
})
```
### package.json
```json
{
  ...
  "scripts": {
    "start": "webpack serve",
    "build": "webpack",
    "dev": "webpack --progress",
+   "analyzer": "webpack-bundle-analyzer --port 8888 ./dist/stats.json"
  },
  ...
}
```


## 上一节
- [如何配置和启动项目](/2020/12/05/webpack_4/)

## 下一节
- [编译时间优化](/2020/12/06/webpack_6/)