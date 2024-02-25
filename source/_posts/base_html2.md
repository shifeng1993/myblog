---
title: 【基础学习-html】2.html元数据
categories: base
date: 2018-10-20 22:18:00
tags:
  - base
  - html
---
# base
指定用于一个文档中包含的所有相对URL的基本URL。
```html
<base target="_blank" href="https://www.shifeng1993.com/">
```

# title
页面的标题
```html
<title>one page</title>
```

# meta
## 标记文档字符串编码
```html
<meta charset="utf-8">
```
  
## seo属性
```html
<meta name="author" content="Jelly Orange">
<meta name="keywords" content="师峰的博客">
<meta name="description" content="师峰的博客,累了歇一歇">
```

## 流量数据统计
加入来源标识
```html
<meta name="referrer" content="origin">
<meta name="referrer" content="no-referrer">
```

## 定义爬虫协议
- index 允许机器人索引页面
- noindex 阻止机器人索引页面
- follow 允许机器人按照页面上的链接
- nofollow 阻止机器人跟踪页面上的链接

```html
<meta name="robots" content="index, follow">

```
## 定义移动设备视口
仅供移动设备使用
- width: 正整数或`device-width`           -> 以px为单位， 定义viewport（视口）的宽度。
- height: 正整数或者字符串`device-height`  -> 以px为单位， 定义viewport（视口）的高度。
- initial-scale: 0.0 到10.0之间的正数     -> 定义设备宽度（纵向模式下的设备宽度或横向模式下的设备高度）与视口大小之间的缩放比率。
- maximum-scale: 0.0 到10.0之间的正数     -> 定义缩放的最大值；它必须大于或等于minimum-scale的值，不然会导致不确定的行为发生。
- minimum-scale: 0.0 到10.0之间的正数     -> 定义缩放的最小值；它必须小于或等于maximum-scale的值，不然会导致不确定的行为发生。
- user-scalable: 布尔值 (yes 或 no）      -> 如果设置为 no，用户将不能放大或缩小网页。默认值为 yes。

```html
<meta name="viewport" content="width=device-width, initial-scale=1">
```

## facebook的metadata
Facebook 编写的元数据协议 Open Graph Data。
```html
<meta property="og:image" content="">
<meta property="og:description" content="师峰的博客,累了歇一歇">
<meta property="og:title" content="师峰的博客">
```
## Twitter 的metadata
```html
<meta name="twitter:title" content="师峰的博客">
```

# link
## 外联样式文件
- media 为[响应式](https://developer.mozilla.org/zh-CN/docs/Web/Guide/CSS/Media_queries)。默认为all

```html
<link rel="stylesheet" media="(max-width: 800px)" href="./index.css">
```

## 表示链接到的文件是 Web App Manifest.
```html
<link rel="manifest" href="/manifest.webmanifest">
```

## 自定义网站图标
```html
<link rel="shortcut icon" href="favicon.ico" type="image/x-icon">
```

## 移动端添加快捷方式网站图标处理
```html
<!-- third-generation iPad with high-resolution Retina display: -->
<link rel="apple-touch-icon-precomposed" sizes="144x144" href="https://img.t.sinajs.cn/t6/style/images/apple/wbfont.svg">
<!-- iPhone with high-resolution Retina display: -->
<link rel="apple-touch-icon-precomposed" sizes="114x114" href="https://img.t.sinajs.cn/t6/style/images/apple/wbfont.svg">
<!-- first- and second-generation iPad: -->
<link rel="apple-touch-icon-precomposed" sizes="72x72" href="https://img.t.sinajs.cn/t6/style/images/apple/wbfont.svg">
<!-- non-Retina iPhone, iPod Touch, and Android 2.1+ devices: -->
<link rel="apple-touch-icon-precomposed" href="https://img.t.sinajs.cn/t6/style/images/apple/wbfont.svg">
<!-- basic favicon -->
<link rel="shortcut icon" href="/favicon.ico">
```

## 新浪微博的自定义图标和移动端快捷方式图标
```html
<link rel="mask-icon" sizes="any" href="//img.t.sinajs.cn/t6/style/images/apple/wbfont.svg" color="black">
<link rel="shortcut icon" type="image/x-icon" href="/favicon.ico">
```

# style
- type: 以MIME类型定义样式语言 默认`type="text/css`;
- media: [响应式](https://developer.mozilla.org/zh-CN/docs/Web/Guide/CSS/Media_queries)。默认为all
- scoped 如果该属性存在，则样式应用于其父元素；如果不存在，则应用于整个文档。(兼容有问题)
- title 指定可选的样式表

## 父级私有
```html
<style>
  html, body{
    margin: 0;
    padding:0;
  }
</style>
```

# script
- type 可选值 `text/javascript`, `text/ecmascript`, `application/javascript`, 和`application/ecmascript`,默认JavaScript

## html4和html5的写法
```html
<!-- HTML4 and (x)HTML -->
<script type="text/javascript" src="index.js">
<!-- HTML5 -->
<script src="index.js"></script>
```
## 异步执行脚本
```html
<script async src="index.js"></script>
```

## 延迟执行脚本
如果写在body之前可以使用defer属性来在文档加载后延迟执行
```html
<script defer src="index.js"></script>
```