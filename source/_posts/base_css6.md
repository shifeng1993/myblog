---
title: 【基础学习-css】6.css盒模型
categories: base
date: 2018-11-05 23:27:00
tags:
  - base
  - css
---
css盒模型是网页布局的基础，每个元素被表示为一个矩形的方框，框的内容、内边距、边界和外边距像洋葱的膜那样，一层包着一层构建起来。

# width 和 height
width 和 height 设置内容框（content box）的宽度和高度。内容框是框内容显示的区域——包括框内的文本内容，以及表示嵌套子元素的其它框。

# padding
padding 表示一个 CSS 框的内边距 ——这一层位于内容框的外边缘与边界的内边缘之间。该层的大小可以通过简写属性padding 一次设置所有四个边，或用 padding-top、padding-right、padding-bottom 和 padding-left 属性一次设置一个边。

# border
CSS 框的边界（border）是一个分隔层，位于内边距的外边缘以及外边距的内边缘之间。边界的默认大小为0——从而让它不可见——不过我们可以设置边界的厚度、风格和颜色让它出现。 border 简写属性可以让我们一次设置所有四个边，例如  border: 1px solid black; 但这个简写可以被各种普通书写的更详细的属性所覆盖：
border-top, border-right, border-bottom, border-left: 分别设置某一边的边界厚度／风格／颜色。
border-width, border-style, border-color: 分别仅设置边界的厚度／风格／颜色，并应用到全部四边边界。
你也可以单独设置某一个边的三个不同属性，如 border-top-width, border-top-style, border-top-color，等。 

# margin
外边距（margin）代表 CSS 框周围的外部区域，称为外边距，它在布局中推开其它 CSS 框。其表现与 padding 很相似；简写属性为 margin，单个属性分别为 margin-top、margin-right、margin-bottom 和 margin-left。

# overflow
当你使用绝对的值设置了一个框的大小（如，固定像素的宽/高），允许的大小可能不适合放置内容，这种情况下内容会从盒子溢流。我们使用overflow属性来控制这种情况的发生。它有一些可能的值，但是最常用的是：

- auto: 当内容过多，溢流的内容被隐藏，然后出现滚动条来让我们滚动查看所有的内容。
- hidden: 当内容过多，溢流的内容被隐藏。
- visible: 当内容过多，溢流的内容被显示在盒子的外边（这个是默认的行为）

# background-clip
background-clip  设置元素的背景（背景图片或颜色）是否延伸到边框下面。

如果没有设置背景颜色或图片，那么这个属性只有在边框（border）设置为透明或半透明时才能看到视觉效果（查看 border-style 或 border-image），不然的话，这个属性造成的样式变化会被边框覆盖住。
- background-clip: border-box     背景延伸到边框外沿（但是在边框之下）。
- background-clip: padding-box    边框下面没有背景，即背景延伸到内边距外沿。
- background-clip: content-box    背景裁剪到内容区 (content-box) 外沿。

# 以上属性的示例代码
```html
<!DOCTYPE html>
<html lang="en">

<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <meta http-equiv="X-UA-Compatible" content="ie=edge">
  <title>盒模型示例代码</title>
  <style>
    * {
      margin: 0;
      padding: 0;
    }

    html,
    body {
      width: 100%;
      height: 100%;
    }

    #root {
      width: 100%;
      height: calc(100% - 20px);
      display: flex;
      flex-direction: column;
      padding: 20px;
      font-size: 20px;
      border: 20px solid rgba(0, 0, 0, 0.5);
    }

    #root header,
    #root footer {
      background-color: blue;
      color: white;
    }

    #root main {
      flex: 1;
      background-color: yellow;
    }

    #root header {
      height: 70px;
    }

    #root footer {
      height: 30px;
      overflow: hidden;
    }

  </style>
</head>

<body>
  <div id="root">
    <header>我是头部</header>
    <main>
      我是中间的容器
    </main>
    <footer>
      <p>这是一段非常长的文字</p>
      <p>这是一段非常长的文字</p>
      <p>这是一段非常长的文字</p>
      <p>这是一段非常长的文字</p>
      <p>这是一段非常长的文字</p>
      <p>这是一段非常长的文字</p>
      <p>这是一段非常长的文字</p>
      <p>这是一段非常长的文字</p>
    </footer>
  </div>
</body>

</html>
```
