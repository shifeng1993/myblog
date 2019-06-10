---
title: 【webpack学习随笔】2.webpack优化配置策略
date: 2019-06-09 21:06:00
tags:
  - base
  - webpack
---
由于webpack各种配置层出不穷，上一篇只是列举了一些常用的配置，而不知道什么时候又会变。。。

剩余的只能按需去查阅文档进行配置咯

这次是一些webpack优化配置策略说明

下面是正文，顺序不分先后

-------------------
# webpack-bundle-analyzer
可视化分析工具，优化可以具体用这个来看包的大小以及分布

# 暴露变量的三种方式
## webpack.ProvidePlugin 注入变量
不会将变量暴露到window上
```js
const webpack = require('webpack');
module.exports = {
  ...
  plugins:[
    new webpack.ProvidePlugin({
      '$': 'jquery',
      '全局变量名': '哪个包'
    })
  ]
}
```

## expose-loader 暴露到window上
帮你把一些变量放到window上

1. 先安装expose-loader
2. 配置loader

注：只要引用一次就会暴露到window上，但是有个缺陷：库会被打包进去bundle文件内。
```js
module.exports = {
  ...
  module:[
    rules:[
      {
        // 当用户引用jquery的话会触发这个loader
        test: require.resolve('jquery'),
        use: {
          loader: 'expose-loader',
          options: '$'
        }
      },
      ...
    ]
  ]
}
```

## 外部变量externals + cdn
添加cdn资源插件 add-asset-html-cdn-webpack-plugin
可以把静态资源放到cdn上
- 可以提高页面加载速度方式
- 打包时候不会被打进去

```js
const AddAssetHtmlCdnWebpackPlugin = require('add-asset-html-cdn-webpack-plugin');

module.exports={
  ...
  plugins:[
    ...
    new AddAssetHtmlCdnWebpackPlugin(true, {
      'jquery': 'cdn地址'
    })
  ]
}
```
如果使用cdn的加载方式 引用的时候不希望打包，最好把他表示成外部变量，在externals内加入
```js
module.exports={
  ...
  externals: {
    'jquery': '$'
  },
  plugins:[
    ...
    new AddAssetHtmlCdnWebpackPlugin(true, {
      'jquery': 'cdn地址'
    })
  ]
}
```

# tree-shaking 消除无用代码
只针对于es6模块 import export，生产环境会自动生效
- import 静态 必须先导入，才能进行使用
- require 动态 可以在代码中实时导入使用

```js
module.exports={
  ...
  optimization: {
    usedExports: true // 在开发模式下标注出有哪些模块被用到，其余没用到的会标识
  }
}
```

副作用：模块没有被引用，但是模块内的方法被执行，则会保留这个模块代码

修复方式，在package.json内加入
```json
{
  ...
  "sideEffects": [
    "*.css" // 配置匹配使用这个副作用的文件
  ], 
  ...
}
```

- 注：不要全部给false，给false会出现另外一个问题，
```json
{
  ...
  "sideEffects": false, // 是否使用这个副作用
  ...
}
```
```js
import 'style.css'
```
没有使用的地方，会把这个模块删掉。

还要使用require()
```js
require('style.css');
```

# scope-hosting(自动，不需要手动优化)
mode为prod时，把变量进行压缩，去提取模块中导出的变量
```js
let a = 1;
let b = 2;
let c = a+b;
console.log(c);
```
scope-hosting后
```js
console.log(3);
```

# 热更新
热更新会保持之前状态，进行更新dom
```js
module.exports = {
  ...
  devServer: {
    hot: true
  },
  ...
  plugins: [
    new webpack.HotModuleReplacementPlugin()
  ]
}
```

有个问题，css-loader自动支持热更新，但是js更改后会刷新浏览器。
使用以下代码
```js
if(module.hot) {
  module.hot.accept()
}
```

```js
if(module.hot) {
  module.hot.accept(['./xx'], function(){
    require('./xx').xxx();
  })
}
```

# 懒加载
import() 动态导入，是实验性的草案，返回一个promise对象，所以可以用async+await 或者promise.then()

需要新增一个插件`@babel/plugin-syntax-dynamic-import`
```js
module.exports = {
  ...
  module:{
    rules:[
      ...
      {
        test:/\.js$/,
        use: {
          loader: 'babel-loader',
          options: {
            presets: ['@babel/preset-env'],
            plugins: [
              '@babel/plugin-syntax-dynamic-import'
            ]
          }
        }
      }
    ]
  }
}
```

- `/* webpackChunkName:"video" */` 可以自定义打包后代码块的名称
- `/* webpackPrefetch:true */`  资源预读取
- `/* webpackPreload:true */`   资源预加载

代码优化： prefetch，preload，dns-prefetch defer async

```js
let button = document.createElement('button');
button = innerHTML = 'button';
button.addEventListener('click',()=>{
  import(/* webpackChunkName:"video" */ 'a.js').then(()=>{
    console.log('a')
  })
})
document.body.appendChild(button);
```

# ignorePlugin
webpack.IgnorePlugin(), 也有另外一个插件 `ContextReplacementPlugin`

例如某个类库下还有引用大量的包，被打包进bundle内，则可以用这个进行忽略

如下是对moment库进行优化
```js
module.exports = {
  ...
  plugins: [
    new webpack.IgnorePlugin(/^\.\/locale$/, /moment$/)
  ]
}
```
最后因为忽略掉了很多文件，导致变为英文，可以用如下方法优化:
```js
import 'moment/locale/zh-cn'; // 在使用的地方再次引入
```

# dllPlugin
先将不需要频繁改变的代码打包好放在那，节约构建时间，一般用在dev环境中，生产环境则一般用splitChunks快速拆分多个包

新建一个`webpack.dll.js`
```js
const webpack = require('webpack');
const path = require('path');

modules.exports = {
  entry: {
    react: ['react', 'react-dom']
  },
  output: {
    library: 'react', // 可以用名称来做对应的var name, 别人需要引用这个react变量
    // libraryTarget: 'commonjs2', // commonjs commonjs2 window this 最常用的一般是commonjs2
    filename: '[name].dll.js'
  },
  plugins: [
    new webpack.DllPlugin({
      name: 'react',
      path: path.resolve(__dirname__,'dist/manifest.json') // 增加映射关系
    })
  ]
}
```

在webpack项目打包的文件配置：

打包时候会在 manifest.json 里面找缓存
```js
const webpack = require('webpack');
const path = require('path');

module.exports = {
  ...
  plugins: [
    new webpack.DllReferencePlugin({
      manifast: path.resolve(__dirname__,'dist/manifest.json')
    })
  ]
}
```

最后会在html里面把这个dll文件以静态资源的方式放进html中

```js
const webpack = require('webpack');
const path = require('path');
const HtmlWebpackPlugin = require('html-webpack-plugin');
const AddAssetHtmlPlugin = require('add-asset-html-webpack-plugin');

module.exports = {
  ...
  plugins: [
    new HtmlWebpackPlugin(),
    new AddAssetHtmlPlugin({
      filepath: './dist/react.dll.js'
    }),
    new webpack.DllReferencePlugin({
      manifast: path.resolve(__dirname,'dist/manifest.json')
    })
  ]
}
```
总结：先产生mainfest.json （name） 和一个js文件全局变量名字一致
引用的时候引用manifest.json 会先去上面查找，找到后，加载对应的内容

# include/exclude
```js
{
  test: /\.js$/,
  use: {
    loader: 'babel-loader',
    exclude: ['node_modules'],  // 排除某些文件
    include: path.resolve(__dirname, 'src'); // 只限定某些文件夹
  }
},
```

# 图片和icon分开打包
需注意svg不是图片，转化成base64会出问题
- icon 走fileloader 包括 woff|svg|eot|ttf 等
- 图片 走urlloader png|jpg 等等

fileloader 只有copy功能，urlloader 包含limit

# splitChunks
一般在生产环境中使用，dev环境一般使用dllplugin
- 实现代码的公用
- 分割第三方模块

```js
module.exports = {
  ...
  optimization: {
    splitChunks: {
        chunks: 'async',  // 默认只分割异步代码块 import() 'async' 'initial' 'all'
        // minSize: 30000,   // 生成块的最小 单位字节
        // maxSize: 0,       // 生成块的最大 单位字节
        // minChunks: 1,     // 分割前必须共享模块的最小块数。
        // maxAsyncRequests: 5,  // 异步代码块最大并发请求数量
        // maxInitialRequests: 3,  // 入口处的最大并发请求数量
        // automaticNameDelimiter: '~', // 生成名称时所用的分隔符
        // name: true,
        cacheGroups: {
          vendors: {
            test: /[\\/]node_modules[\\/]/,
            priority: -10  // 优先级
          },
          // default是打包出来的名称，可以修改，例如改成common
          default: {
            minChunks: 2,
            minSize: 0, // 上面的minsize是默认的配置，这个minsize是自己配置的，所以优先级高
            priority: -20, // 优先级
            reuseExistingChunk: true
          },
          // 第三方库分离示例 匹配react 和 react-dom，完成打包的分离
          react: {
            test: /[\\/]node_modules[\\/]react|react-dom/,
            priority: -10  // 优先级
          },
        }
      }
    }
  },
}
```

# resolve
可以减少查找文件的范围
```js
module.exports = {
  ...
  resolve: {
    extensions: ['.vue','.js','css','json','jsx'], // 扩展名的查找顺序
    mainFiles: ['index', 'main'], // 解析目录时要使用的文件名
    mainFields: ['style', 'main'], // 在 package.json 中使用哪个字段导入模块。
    modules: ['node_modules'], // 告诉 webpack 解析模块时应该搜索的目录。
    alias: { // 别名
      '@': path.resolve(__dirname,'src');
    }
  }
}
```

# devtool
https://webpack.docschina.org/configuration/devtool/

一般来讲做以下配置
- dev   "cheap-module-source-map" || "cheap-module-eval-source-map"
- prod  "source-map"

开发环境一般有需要就是用以上配置，没需要就不做配置

生产环境一般会使用source-map，但是会配置不让普通用户访问

```js
module.exports = {
  devtool: 'source-map'
}
```

# 多线程打包 happypack
项目比较大的时候会使用，因为开进程比较浪费时间。

# 总结
比较有效的优化手段

- CDN
- dllPlugin
- splitChunks
- happypack
