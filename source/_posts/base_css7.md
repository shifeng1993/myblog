---
title: 【基础学习-css】7.css字体样式和文本布局
categories: base
date: 2018-11-08 23:27:00
tags:
  - base
  - css
---
这几天，加班的疲劳和南窑头的寒冷封印了我。今天晚上请了三线小时假，终于可以继续写了。。
# 字体样式
## color
文字的前景色
```css
p {
  color: red;
}
```

## font-family

### 网页安全字体
一般来说通用的字体种类 是网页安全字体

以下是一些通用字体：

| 字体名称             | 字体类型     | 备注 |
| -------------    | -------  | ------------- |
| Arial          | sans-serif      | 通常认为最佳做法还是添加 Helvetica 作为 Arial 的首选替代品，尽管它们的字体面几乎相同，但 Helvetica 被认为具有更好的形状，即使Arial更广泛地可用。  |
| Courier New        | monospace      | 某些操作系统有一个 Courier New 字体的替代（可能较旧的）版本叫Courier。使用Courier New作为Courier的首选替代方案，被认为是最佳做法。 |
| Georgia          | serif    | |
| Times New Roman     | serif   | 某些操作系统有一个 Times New Roman 字体的替代（可能较旧的）版本叫 Times。使用Times作为Times New Roman的首选替代方案，被认为是最佳做法。 |
| Trebuchet MS     |sans-serif     | 您应该小心使用这种字体——它在移动操作系统上并不广泛。  |
| Verdana    |sans-serif     |   |

### 默认字体
CSS 定义了 5 个常用的字体名称:  serif, sans-serif, monospace, cursive,和 fantasy. 这些都是非常通用的，当使用这些通用名称时，使用的字体完全取决于每个浏览器，而且它们所运行的每个操作系统也会有所不同。

| 名称 |	定义	|示例
| -------------    | -------  | ------------- |
| serif |	有衬线的字体 |（衬线一词是指字体笔画尾端的小装饰，存在于某些印刷体字体中）|	<p style="font-family:serif;">My big red elephant</p>|
| sans-serif | 	没有衬线的字体。|	<p style="font-family:sans-serif;">My big red elephant</p>|
| monospace	|每个字符具有相同宽度的字体，通常用于代码列表。|	<p style="font-family:monospace;">My big red elephant</p>|
| cursive	|用于模拟笔迹的字体，具有流动的连接笔画。|	<p style="font-family:cursive;">My big red elephant</p>|
| fantasy	|用来装饰的字体	|<p style="font-family:fantasy;">My big red elephant</p>|

### 字体栈
由于不能保证所有电脑上都有想要的字体，可以提供一个字体栈 (font stack)，这样的话，浏览器就有多种字体可以选择了。只需包含一个font-family属性，其值由几个用逗号分离的字体名称组成。

例如：
```css
p {
  font-family:cursive, sans-serif;
}
```

## font-size
字体大小常用单位有 px em rem，需要注意rem支持版本是ie9以上（包括ie9）

元素的 font-size 属性是从该元素的父元素继承的，如果没有设定默认是16px;

例如：
```css
p {
  font-size: 12px;
}
/* 或者 */
p {
  font-size: 1em;
}
/* 或者 */
p {
  font-size: 1rem;
}
```

## font-style
用来打开和关闭文本 italic (斜体)。
- normal: 将文本设置为普通字体 (将存在的斜体关闭)
- italic: 如果当前字体的斜体版本可用，那么文本设置为斜体版本；如果不可用，那么会利用 oblique 状态来模拟 italics。
- oblique: 将文本设置为斜体字体的模拟版本，也就是将普通文本倾斜的样式应用到文本中。

## font-weight
设置文字的粗体大小。
- normal, bold: 普通或者加粗的字体粗细
- lighter, bolder: 将当前元素的粗体设置为比其父元素粗体更细或更粗一步。
- 100–900: 数值粗体值，如果需要，可提供比上述关键字更精细的粒度控制。

## text-transform
允许你设置要转换的字体。
- none: 防止任何转型。
- uppercase: 将所有文本转为大写。
- lowercase: 将所有文本转为小写。
- capitalize: 转换所有单词让其首字母大写。
- full-width: 将所有字形转换成固定宽度的正方形，类似于等宽字体，允许对齐。拉丁字符以及亚洲语言字形（如中文，日文，韩文）

## text-decoration
设置/取消字体上的文本装饰。比如a标签的下划线。
- none: 取消已经存在的任何文本装饰
- underline: 文本下划线
- overline: 文本上划线
- line-through: 穿过文本的线

## text-shadow
```css
text-shadow: 4px 4px 5px red;

text-shadow: -1px -1px 1px #aaa,
             0px 4px 1px rgba(0,0,0,0.5),
             4px 4px 5px rgba(0,0,0,0.7),
             0px 0px 7px rgba(0,0,0,0.4);
```
文本阴影从左到右的具体值：
1. 阴影与原始文本的水平偏移，可以使用大多数的 CSS 单位 length and size units, 但是 px 是比较合适的。这个值必须指定。
2. 阴影与原始文本的垂直偏移;效果基本上就像水平偏移，除了它向上/向下移动阴影，而不是左/右。这个值必须指定。
3. 模糊半径 - 更高的值意味着阴影分散得更广泛。如果不包含此值，则默认为0，这意味着没有模糊。可以使用大多数的 CSS 单位 length and size units.
4. 阴影的基础颜色，可以使用大多数的 CSS 颜色单位 CSS color unit. 如果没有指定，默认为 black.

## 其他字体样式
- font-variant: 在小型大写字母和普通文本选项之间切换。
- font-kerning: 开启或关闭字体间距选项。
- font-feature-settings: 开启或关闭不同的 OpenType 字体特性。
- font-variant-alternates: 控制给定的自定义字体的替代字形的使用。
- font-variant-caps: 控制大写字母替代字形的使用。
- font-variant-east-asian: 控制东亚文字替代字形的使用, 像日语和汉语。
- font-variant-ligatures: 控制文本中使用的连写和上下文形式。
- font-variant-numeric: 控制数字，分式和序标的替代字形的使用。
- font-variant-position: 控制位于上标或下标处，字号更小的替代字形的使用。
- font-size-adjust: 独立于字体的实际大小尺寸，调整其可视大小尺寸。
- font-stretch: 在给定字体的可选拉伸版本中切换。
- text-underline-position: 指定下划线的排版位置，通过使用 text-decoration-line 属性的underline 值。
- text-rendering: 尝试执行一些文本渲染优化。


# 文本布局
## text-align
文本对齐方式有以下值：
- left: 左对齐文本
- right: 右对齐文本
- center: 居中文字
- justify: 使文本展开，改变单词之间的差距，使所有文本行的宽度相同

## line-height
行高可以设置固定单位比如px 也可以设置乘数表示

例如：
```css
line-height: 1.5;
```

## 字间距和词间距
```css
p::first-line {
  letter-spacing: 2px;
  word-spacing: 4px;
}
```

## 其他文本布局样式
- text-indent: 指定文本内容的第一行前面应该留出多少的水平空间。
- text-overflow: 定义如何向用户表示存在被隐藏的溢出内容。
- white-space: 定义如何处理元素内部的空白和换行。
- word-break: 指定是否能在单词内部换行。
- direction: 定义文本的方向 (这取决于语言，并且通常最好让HTML来处理这部分，因为它是和文本内容相关联的。)
- hyphens: 为支持的语言开启或关闭连字符。
- line-break: 对东亚语言采用更强或更弱的换行规则。
- text-align-last: 定义一个块或行的最后一行，恰好位于一个强制换行前时，如何对齐。
- text-orientation: 定义行内文本的方向。
- word-wrap: 指定浏览器是否可以在单词内换行以避免超出范围。
- writing-mode: 定义文本行布局为水平还是垂直，以及后继文本流的方向。

# font简写
许多字体的属性也可以通过 font 的简写方式来设置 . 这些是按照以下顺序来写的：  font-style, font-variant, font-weight, font-stretch, font-size, line-height, and font-family.

如果你想要使用 font 的简写形式，在所有这些属性中，只有 font-size 和 font-family 是一定要指定的。

font-size 和 line-height 属性之间必须放一个正斜杠。

例如：
```css
p {
  font: italic normal bold normal 3em/1.5 Helvetica, Arial, sans-serif;
}
```