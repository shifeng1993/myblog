---
title: 【基础学习-css】3.css伪类选择器和伪元素
categories: base
date: 2018-10-31 0:33:00
tags:
  - base
  - css
---
# 伪类选择器
css伪类是一个以冒号`:`作为前缀，被添加到一个选择器末尾的关键字，当你希望样式在特定状态下才被呈现到指定的元素时，你可以往元素的选择器后面加上对应的伪类。你可能希望某个元素在处于某种状态下呈现另一种样式，例如当鼠标悬停在元素上面时，或者当一个复选框被禁用或被勾选时，又或者当一个元素是它在 DOM 树中父元素的第一个子元素时。

一共有如下伪类选择器：
- css2.1中的伪类选择器
```
:first-child          父元素的第一个子元素
:link                 所有未被点击的链接
:visited              所有已被点击的链接
:active	              鼠标已经其上按下、还没有释放的元素
:hover	              鼠标悬停其上的元素
:focus	              获得当前焦点的元素
:lang(c)              lang属性等于c的元素
:first                打印文档的时候，第一页的样式
```
- css3中的伪类选择器
```
:enabled              匹配表单中激活的元素
:disabled             匹配表单中禁用的元素
:checked              匹配表单中被选中的radio（单选框）或checkbox（复选框）元素
:root                 匹配文档的根元素，对于HTML文档，就是HTML元素
:nth-child(n)         匹配其父元素的第n个子元素，第一个编号为1
:nth-last-child(n)    匹配其父元素的倒数第n个子元素，第一个编号为1
:nth-of-type(n)       与:nth-child()作用类似，但是仅匹配使用同种标签的元素
:nth-last-of-type(n)  与:nth-last-child() 作用类似，但是仅匹配使用同种标签的元素
:last-child           匹配父元素的最后一个子元素，等同于:nth-last-child(1)
:first-of-type        匹配父元素下使用同种标签的第一个子元素，等同于:nth-of-type(1)
:last-of-type         匹配父元素下使用同种标签的最后一个子元素，等同于:nth-last-of-type(1)
:only-child           匹配父元素下仅有的一个子元素，等同于:first-child:last-child或 :nth-child(1):nth-last-child(1)
:only-of-type         匹配父元素下使用同种标签的唯一一个子元素，等同于:first-of:last-of-type或 :nth-of-type(1):nth-last-of-type(1)
:empty                匹配一个不包含任何子元素的元素，注意，文本节点也被看作子元素
:not(s)               匹配不符合当前选择器的任何元素
:target               匹配文档中特定"id"点击后的效果
:default              表示一组相关元素中的默认表单元素
:indeterminate        表示状态不确定的表单元素
:left/right           需要和@规则 @page 配套使用, 对打印文档的左侧页设置CSS样式.目前只有ie和opera支持
  @page :left {
    margin: 2in 3in;
  }
:required             表示任意拥有required属性的 <input> 或 <textarea> 元素使用它. 它允许表单在提交之前容易的展示必填字段并且渲染其外观. 
:optional             表示任意没有required属性的 <input>，<select> 或  <textarea> 元素使用它。
```

伪类选择器使用：

html
```html
<a href="https://www.baidu.com" target="_blank">Baidu</a>
```

```css
/* 这些样式将在任何情况下应用于我们
的链接 */

a {
  color: blue;
  font-weight: bold;
}

/* 我们想让被访问过的链接和未被访问
的链接看起来一样 */

a:visited {
  color: blue;
}

/* 当光标悬停于链接，键盘激活或锁定
链接时，我们让链接呈现高亮 */

a:hover,
a:active,
a:focus {
  color: darkred;
  text-decoration: none;
}
```

# 伪元素
伪元素跟伪类很像，但它们又有不同的地方。它们都是关键字，但这次伪元素前缀是两个冒号`::`， 同样是添加到选择器后面去选择某个元素的某个部分。

css2 和 css3 伪元素写法：css2是用一个冒号，css3使用两个冒号
一共有以下伪类：
```
::after                   最后一个子元素
::before                  第一个子元素
::first-letter            块级元素第一行第一个字
::first-line              块级元素第一行 
```
