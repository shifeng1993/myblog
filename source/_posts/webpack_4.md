---
title: 【webpack学习随笔】2.如何配置和启动项目
date: 2020-12-05 15:14:00
tags:
  - base
  - webpack
---

## 安装依赖
```bash
npm install webpack webpack-cli html-webpack-plugin webpack-dev-server cross-env -D
```

## 新建webpack配置文件
在项目根目录下新建webpack.config.js

```js
const path = require('path');
const HtmlWebpackPlugin = require('html-webpack-plugin');

module.exports = {
  mode: 'development',
  context: process.cwd(),
  devtool: "source-map",
  entry: {
    main: './src/index.js'
  },
  output: {
    path: path.resolve(__dirname, 'dist'),
    filename: '[name].js'
  },
  plugins: [
    new HtmlWebpackPlugin()
  ]
}
```

## 配置启动服务和构建命令
```json
{
  ...
  "scripts": {
    "start": "webpack serve",
    "build": "webpack"
  },
  ...
}
```

## 配置完成后测试
- 启动服务 
```bash
npm start
```

- 构建
```bash
npm run build
```

## 下一节
- [如何进行数据和性能分析](/2020/12/05/webpack_5/)