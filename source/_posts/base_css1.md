---
title: 【基础学习-css】1.css结构以及基础语法
categories: base
date: 2018-10-29 22:02:00
tags:
  - base
  - css
---
css是一种声明式语言，通过属性 和属性值的 key-value形式来定义样式。

# css声明
一个声明，中间用英文半角`:`隔开，需注意：如果属性或值有一个无效，该声明就是无效，会被浏览器自动忽略。
```css
background: #fff;
```

# css声明块
声明按块分组，每组都用一对大括号包裹，声明块里的每一个声明必须用半角分号（;）分隔，否则代码会不生效（至少不会按预期结果生效）。声明块里的最后一个声明结束的地方，不需要加分号，但是最后加分号是个好习惯，因为可以防止在后续增加声明时忘记加分号。
```css
{
  color: #000;
  font-size: 18px;
}
```

# css选择器
css选择器能在页面上匹配一些元素，来对应声明块是应用到哪个元素。
选择器有以下分类：

名称超链接可以点击进入详细

- [简单选择器]()：通过元素类型、class 或 id 匹配一个或多个元素。
- 属性选择器：通过 属性/属性值 匹配一个或多个元素。
- 伪类：匹配处于确定状态的一个或多个元素，比如被鼠标指针悬停的元素，或当前被选中或未选中的复选框，或元素是DOM树中一父节点的第一个子节点。
- 伪元素:匹配处于相关的确定位置的一个或多个元素，例如每个段落的第一个字，或者某个元素之前生成的内容。 
- 组合器：这里不仅仅是选择器本身，还有以有效的方式组合两个或更多的选择器用于非常特定的选择的方法。例如，你可以只选择divs的直系子节点的段落，或者直接跟在headings后面的段落。
- 多重选择器：这些也不是单独的选择器；这个思路是将以逗号分隔开的多个选择器放在一个CSS规则下面， 以将一组声明应用于由这些选择器选择的所有元素。

## css选择器优先级：

共分为4个等级：

- 第一等：代表内联样式，如: style="xxx"，权值为1000。
- 第二等：代表ID选择器，如：#content，权值为100。
- 第三等：代表类，伪类和属性选择器，如.content，:hover，[attribute]，权值为10。
- 第四等：代表元素选择器和伪元素选择器，如div，p，权值为1。

选择器的权值加到一起，大的优先；如果权值相同，后定义的优先。

important > 内联 > ID > 类 > 标签 | 伪类 | 属性选择 > 伪对象 > 通配符 > 继承

## css选择器执行效率
1. id选择器（#myid）
2. 类选择器（.myclassname）
3. 标签选择器（div,h1,p）
4. 相邻选择器（h1+p）
5. 子选择器（ul < li）
6. 后代选择器（li a）
7. 通配符选择器（*）
8. 属性选择器（a[rel="external"]）
9. 伪类选择器（a:hover,li:nth-child）

# css语句
`@` 规则 在CSS中被用来传递元数据、条件信息或其它描述性信息。

例如：
```css
@import 'base.css';
```

有如下`@`规则：

- @charset, 定义样式表使用的字符集.
- @import, 告诉 CSS 引擎引入一个外部样式表.
- @namespace, 告诉 CSS 引擎必须考虑XML命名空间。
- @media, 如果满足媒介查询的条件则条件规则组里的规则生效。
- @page, 描述打印文档时布局的变化.
- @font-face, 描述将下载的外部的字体。 
- @keyframes, 描述 CSS 动画的中间步骤。
- @supports, 如果满足给定条件则条件规则组里的规则生效。 


# css注释
CSS中的注释以`/*`开始并以`*/`结束
```css
/* 基本元素样式 */
/* -------------------------------------------------------------------------------------------- */
body {
  font: 1em/150% Helvetica, Arial, sans-serif;
  padding: 1em;
  margin: 0 auto;
  max-width: 33em;
}

@media (min-width: 70em) {

  /* 特别指明全局字体大小，在大屏或大窗口下加大字体大小以增加可读性 */
  body {
    font-size: 130%;
  }
}

h1 {
  font-size: 1.5em;
}

/* 处理嵌套在DOM中的特定元素  */
/* -------------------------------------------------------------------------------------------- */
div p,
#id:first-line {
  background-color: red;
}

div p {
  margin: 0;
  padding: 1em;
}

div p+p {
  padding-top: 0;
}

```

# 简写
一些属性比如 font，background，padding，border，和 margin 被称为简写属性。

在padding和margin这样的简写属性中，值赋值的顺序是top、right、bottom、left。
它们还有其他简写方式，例如给padding两个值，则第一个值表示top/bottom，第二个值表示left/right

background也一样。
```css
{
  padding-top: 10px;
  padding-right: 15px;
  padding-bottom: 15px;
  padding-left: 5px;
}

{
  padding: 10px 15px 15px 5px;
}

{
  padding: 10px 0;
}

{
  background-color: red;
  background-image: url(bg-graphic.png);
  background-position: 10px 10px;
  background-repeat: repeat-x;
  background-scroll: fixed;
}

{
  background: red url(bg-graphic.png) 10px 10px repeat-x fixed;
}
```