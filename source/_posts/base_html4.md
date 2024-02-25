---
title: 【基础学习-html】4.html超链接和URL
categories: base
date: 2018-10-22 22:02:00
tags:
  - base
  - html
---
# 超链接
超链接可以指向HTML文件、文本文件、图像、文本文档、视频和音频文件以及可以在网络上保存的任何其他内容。
- 使用`a`标签将文本或块级转换为超链接

## 文本
```html
<p><a href="https://www.baidu.com">点击跳转到百度</a></p>
```

## 块级
```html
<a href="https://www.baidu.com">
  <img src="https://www.baidu.com/img/bd_logo1.png" alt="baidu">
</a>
```

# URL (统一资源定位符)
## 指向同级目录
```html
<p><a href="equal.html">同级html</a></p>
<p><a href="./equal.html">同级html</a></p>
```

## 指向父级目录
```html
<p><a href="../parent.html">父级html</a></p>
```

## 指向子级目录
```html
<p><a href="child/index.html">子级html</a></p>
<p><a href="./child/index.html">子级html</a></p>
```

## 链接到文档片段
使用 `#` 加html元素id属性跳转
```html
  <h2 id="first">第一</h2>
  <p><a href="#fourth">点击跳第四</a></p>
  <p>1</p>
  <p>1</p>
  <p>1</p>
  <p>1</p>
  <p>1</p>
  <h2 id="second">第二</h2>
  <p>1</p>
  <p>1</p>
  <p>1</p>
  <p>1</p>
  <p>1</p>
  <p>1</p>
  <h2 id="third">第三</h2>
  <p>1</p>
  <p>1</p>
  <p>1</p>
  <p>1</p>
  <p>1</p>
  <p>1</p>
  <h2 id="fourth">第四</h2>
  <p>1</p>
  <p>1</p>
  <p>1</p>
  <p>1</p>
  <p>1</p>
  <p>1</p>
```

## 绝对链接和相对链接
绝对URL： 指向由其在Web上的绝对位置定义的位置，包括 协议 and 域名. 像下面的例子,如果index.html 页面上传到projects这一个目录 。project位于web服务站点的根目录, web站点的域名为http://www.example.com, 这个页面可以通过http://www.example.com/projects/index.html访问 ( 或者仅仅通过http://www.example.com/projects/来访问, 因为大多数web服务通过访问index.html这样的页面来加载，如果没有特定的URL的话)

绝对URL总是指向相同的位置，不管它在哪里使用。

相对URL： 指向与您链接的文件相关的位置，更像我们在前面一节中所看到的位置。例如，如果我们想从示例文件链接http://www.example.com/projects/index.html转到相同目录下的一个PDF文件, URL就是文件名URL — 例如 project-brief.pdf —没有其他的信息要求. 如果PDF文件能够在projects的子目录pdfs中访问到, 相对路径就是pdfs/project-brief.pdf (对应的绝对URL就是 http://www.example.com/projects/pdfs/project-brief.pdf.)

一个相对URL将指向不同的位置，这取决于它所在的文件所在的位置——例如，如果我们把index.html 文件 从 projects 目录移动出来并进入Web站点的根目录（最高级别，而不是任何目录中），  pdfs/project-brief.pdf 的相对URL将会指向http://www.example.com/pdfs/project-brief.pdf, 而不是http://www.example.com/projects/pdfs/project-brief.pdf

在平时使用时，尽可能使用相对链接
- 首先，检查代码要容易得多——相对URL通常比绝对URL短得多，这使得阅读代码更容易。
- 其次，在可能的情况下使用相对URL更有效。当使用绝对URL时，浏览器首先通过查询域名（使用“DNS”）}查找服务器的真实位置，然后再转到该服务器并查找所请求的文件。另一方面，相对URL，浏览器只在同一服务器上查找被请求的文件。因此，如果你使用相对URL做的绝对URL，你就不断地让你的浏览器做额外的工作，这意味着它的效率会降低。

## 下载时使用下载属性
```html
<a href="https://www.baidu.com"
   download="xxx.apk">
  下载xxx
</a>
```

## 模式和协议
- http——超文本传输协议资源
- https——用安全套接字层传送的超文本传输协议
- ftp——文件传输协议
- mailto——电子邮件地址
- ldap——轻型目录访问协议搜索
- file——当地电脑或网上分享的文件
- news——Usenet新闻组
- gopher——Gopher协议
- telnet——Telnet协议

## 大小写
unix上的url是区分大小写的，但是windows上是不区分大小写的。