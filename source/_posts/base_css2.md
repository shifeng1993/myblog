---
title: 【基础学习-css】2.css简单选择器
categories: base
date: 2018-10-29 23:02:00
tags:
  - base
  - css
---
# 元素选择器
选择器名和指定的HTML元素名的不区分大小写的匹配

html:
```html
<h1>css部分</h1>
```

css:
```css
h1 {
  color: #000;
  font-size: 18px;
}
```

# 类选择器
选择器名对元素class的匹配。 使用英文句号`.` + class 名进行匹配。

html:
```html
<h1 class="title">css部分</h1>
```

css:
```css
.title {
  color: #000;
  font-size: 18px;
}
```

# id选择器
选择器名对元素id的匹配。 使用英文哈希符号`#`+ id 名进行匹配。

需要注意的是一个ID名称必须在当前文件中是唯一的，重复则不可预测，例如一些浏览器只识别第一个id名
html:
```html
<h1 id="title">css部分</h1>
```

css:
```css
#title {
  color: #000;
  font-size: 18px;
}
```

# 通用选择器
使用`*`对当前文件所有html元素的通配。

比较常用的是一个对所有元素去掉 margin padding 的用法
```css
* {
  margin: 0;
  padding: 0;
}
```