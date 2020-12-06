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

## Tree Shaking（树摇晃）
- 一个模块中多个方法，只要有用到的方法，则整个文件都会被打到bundle里面，tree shaking 就是只把有用的方法打包进bundle，没用的方法会在 uglify 阶段擦除掉
- 原理是：利用es6模块的特点，只能作为模块顶层语句出现，import的模块名只能是字符串常量
  
### 开启
- mode 为 production 时默认开启
- 在package.json 中配置 
  - "sideEffects": "false" 所有的代码都没副作用（都可以进行treeshaking）
  - 可能会把css和@babel/polyfill文件干掉，可以设置 "sideEffects": ["*.css"]

### treeshaking的四种情况
- 没有导入和使用(es6)
- 代码不会被执行，不可到达
- 代码执行结果不会被用到
- 代码中只写不读的变量


## scope hoisting（作用域提升）
- scope hoisting 可以让webpack打包出来的代码变得更小，运行的更快，webpack3+支持
- 原理是将所有的模块按照引用顺序放在一个函数作用域内，然后适当的重命名一些变量，防止命名冲突
- 在mode 是production下默认开启，开发环境需要使用webpack.optimizeModuleConcatenationPlugin插件

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
const OptimizeCssAssetsWebpackPlugin = require('optimize-css-assets-webpack-plugin');
const TerserWebpackPlugin = require('terser-webpack-plugin');

// css和js的加载可以并行，所以我们可以通过此插件提取css成单独的文件，然后去掉无用的css并进行压缩
const MiniCssExtractPlugin = require('mini-css-extract-plugin');
const PurgecssWebpackPlugin = require('purgecss-webpack-plugin');

const glob = require('glob');
const webpack = require('webpack');

const PATHS = {
  src: path.resolve(__dirname, 'src')
}

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
  optimization: {
    minimize: true,   // 开启最小化
    minimizer: [
      new TerserWebpackPlugin() // 压缩js
    ]
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
              MiniCssExtractPlugin.loader,
              'css-loader'
            ]
          },
          {
            test: /\.less$/,
            use: [
              MiniCssExtractPlugin.loader,
              'css-loader',
              'less-loader'
            ]
          }
        ]
      },
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
  plugins: [
    new HtmlWebpackPlugin({
      template: './src/index.html',
      minify: { // 压缩html
        collapseWhitespace: true, // 去掉空格
        removeComments: true // 去掉注释
      }
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
    }),
    // 压缩css
    new OptimizeCssAssetsWebpackPlugin(),
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
