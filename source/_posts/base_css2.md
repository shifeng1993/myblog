---
title: 【基础学习-css】2.css简单选择器和属性选择器
categories: base
date: 2018-10-30 22:02:00
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

# 属性和值选择器

- [attr]：该选择器选择包含 attr 属性的所有元素，不论 attr 的值为何。 (css2.1)
- [attr=val]：该选择器仅选择 attr 属性被赋值为 val 的所有元素。 (css2.1)
- [attr~=val]：该选择器仅选择具有 attr 属性的元素，而且要求 val 值是 attr 值包含的被空格分隔的取值列表里中的一个。 (css2.1)

```css
/* 所有具有"data-vegetable"属性的元素将被应用绿色的文本颜色 */
[data-vegetable] {
  color: green
}

/* 所有具有"data-vegetable"属性且属性值刚好为"liquid"的元素将被应用金色的背景颜色 */
[data-vegetable="liquid"] {
  background-color: goldenrod;
}

/* 所有具有"data-vegetable"属性且属性值包含"spicy"的元素，
即使元素的属性中还包含其他属性值，都会被应用红色的文本颜色 */
[data-vegetable~="spicy"] {
  color: red;
}
```
# 子字符串值选择器

- [attr|=val] : 选择attr属性的值是 val 或值以 val- 开头的元素（注意，这里的 “-” 不是一个错误，这是用来处理语言编码的）。 (css2.1)
- [attr^=val] : 选择attr属性的值以 val 开头（包括 val）的元素。 (css3)
- [attr$=val] : 选择attr属性的值以 val 结尾（包括 val）的元素。 (css3)
- [attr*=val] : 选择attr属性的值中包含子字符串 val 的元素（一个子字符串就是一个字符串的一部分而已，例如，”cat“ 是 字符串 ”caterpillar“ 的子字符串）。 (css3)

```css
/* 语言选择的经典用法 */
[lang|="fr"] {
  font-weight: bold;
}

/* 
    具有"data-vegetable"属性含有值"not spicy"的所有元素,都变回绿色
*/
[data-vegetable*="not spicy"] {
  color: green;
}

/* 
   具有"data-quantity"属性其值以"kg"结尾的所有元素*/
[data-quantity$="kg"] {
  font-weight: bold;
}

/* 
   具有属性"data-quantity"其值以"optional"开头的所有元素 
*/
[data-quantity^="optional"] {
  opacity: 0.5;
}
```

