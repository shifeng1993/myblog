---
title: 【基础学习-html】html基础
categories: base
date: 2018-10-19
tags:
  - base
  - html
---

html 不是一种编程语言，而是一种标记语言。

# html元素组成
一个html元素由 开始标签，结束标签和内容构成
例如:
```html
<p>hello world</p>
```
- 元素`<p>hello world</p>`
- 开始标签`<p>`
- 结束标签`</p>`
- 内容`hello world`

# html元素嵌套
```html
<p>
  do you
  <strong>
    speak
  </strong>
  english?
</p>
```
嵌套必须需是有始有终，开始标签和结束标签是相对的。

# 块级元素和行内(内联)元素

## 块级元素特点：
- 独占一行，在默认情况下，宽度自动填满父元素宽度
- 宽度和高度可以控制
- margin和padding横向纵向设置都有效
- 除个别特殊元素外，可包含块级元素和行内元素

## 行内元素特点：
- 不能独占一行
- 宽高由内容撑开
- margin和padding横向设置有效，纵向设置不产生边距效果
- 除ins和del外，不能包含块级元素

## 转换
- 行内元素通过`display: block;` 转换为块级元素
- 块级元素可以通过`display: inline;` 转换为行内元素
- 两者都可以通过 `display: inline-block;` 转换为行内块级元素

## 常见的行内和块级元素
- 行内 a、b、br、button、del、em、embed、i、iframe、img、input、ins、label、map、script、strong、select、slot、small、sub、svg、template、time、textarea
- 块级 div、p、ul、ol、li、dl、dd、p、h1-h6、blockquote、address、article、aside、audio、canvas、fieldset、figcaption、figure、header、footer、form、hgroup、hr、noscript、pre、table、tfoot、video

# head内元数据
## title
页面的标题
```
<head>
  <meta charset="utf-8">
  <title>one page</title>
</head>
```

## meta
