---
title: 【webpack学习随笔】5.编译体积优化
date: 2020-12-06 13:52:00
tags:
  - base
  - webpack
---
## 压缩js、css、html和图片等资源
- optimize-css-assets-webpack-plugin 压缩和优化css资源
- terser-webpack-plugin 优化和压缩js资源
- image-webpack-loader 对图片进行压缩和优化

### 安装
```bash
npm install optimize-css-assets-webpack-plugin terser-webpack-plugin image-webpack-loader -D
```

### webpack.config.js
```js
const OptimizeCssAssetsWebpackPlugin = require('optimize-css-assets-webpack-plugin');
const TerserWebpackPlugin = require('terser-webpack-plugin');
module.exports = smw.wrap({
  optimization: {
    minimize: true,   // 开启最小化
    minimizer: [
      new TerserWebpackPlugin() // 压缩js
    ]
  },
  module:{
    rules:[
       {
        test: /\.(gif|png|jpe?g|svg)$/i,
        use: [
          'file-loader',
          {
            loader: 'image-webpack-loader',
            // 配置优化图片
            options: {
              mozjpeg: {
                progressive: true,
              },
              // optipng.enabled: false will disable optipng
              optipng: {
                enabled: false,
              },
              pngquant: {
                quality: [0.65, 0.90],
                speed: 4
              },
              gifsicle: {
                interlaced: false,
              },
              webp: {
                quality: 75
              }
            }
          },
        ],
      }
    ]
  },
  plugins:[
    new HtmlWebpackPlugin({
      template: './src/index.html',
      minify: { // 压缩html
        collapseWhitespace: true, // 去掉空格
        removeComments: true // 去掉注释
      }
    }),
    // 压缩css
    new OptimizeCssAssetsWebpackPlugin()
  ]
})
```

## 清除无用的css
- purgecss-webpack-plugin 单独提取css并清除用不到的css

### 安装
```bash
npm install purgecss-webpack-plugin mini-css-extract-plugin -D
```

### webpack.config.js
```js
// css和js的加载可以并行，所以我们可以通过此插件提取css成单独的文件，然后去掉无用的css并进行压缩
const MiniCssExtractPlugin = require('mini-css-extract-plugin');
const PurgecssWebpackPlugin = require('purgecss-webpack-plugin');

const glob = require('glob');
const webpack = require('webpack');

const PATHS = {
  src: path.resolve(__dirname, 'src')
}

module.exports = smw.wrap({
   module: {
    rules: [
       {
        oneOf: [
          {
            test: /\.css$/,
            use: [
              MiniCssExtractPlugin.loader,  // MiniCssExtractPlugin.loader替换掉style-loader
              'css-loader'
            ]
          },
          {
            test: /\.less$/,
            use: [
              MiniCssExtractPlugin.loader, // MiniCssExtractPlugin.loader替换掉style-loader
              'css-loader',
              'less-loader'
            ]
          }
        ]
      },
    ]
  },
  plugins: [
    // 提取css
    new MiniCssExtractPlugin({
      filename: '[name].css'
    }),
    // **匹配任意字符，包括路径分隔符 *匹配任意字符，不包括路径分隔符
    new PurgecssWebpackPlugin({
      paths: glob.sync(`${PATHS.src}/**/*`, {nodir: true})
    })
  ]
})
```

## 上一节
- [编译时间优化](/2020/12/06/webpack_6/)

## 下一节
- [运行速度优化](/2020/12/06/webpack_8/)
