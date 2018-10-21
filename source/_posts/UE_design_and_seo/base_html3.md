---
title: 【基础学习-html】3.html文档语义和结构
categories: base
date: 2018-10-21 21:45:00
tags:
  - base
  - html
---
语义化html，对可访问性，SEO（搜索引擎优化）等非常重要

# 标题和段落
- 标题使用h1-h6标签
- 块级元素p可以区分段落

段落和标题可以使文本更加结构化和语义化
```html
<h1>hello</h1>
<p>Hello, it's me</p>
<p>I was wondering if after all these years you'd like to meet</p>
```

# 列表

## 无序列表 (ul)
无序列表使用ul标签来包裹，li是每一项
```html
<ul>
  <li>牛奶</li>
  <li>鸡蛋</li>
  <li>面包</li>
  <li>火腿</li>
</ul>
```

## 有序列表 (ol)
无序列表使用ol标签来包裹，li是每一项
```html
<ol>
  <li>牛奶</li>
  <li>鸡蛋</li>
  <li>面包</li>
  <li>火腿</li>
</ol>
```

## 描述列表 (dl)
dt是标题，dd对标题的描述
```html
<dl>
  <dt>天真</dt>
  <dd>吴邪是南派三叔的作品《盗墓笔记》中的主人公，是《盗墓笔记》的核心，铁三角的精神领袖。生于盗墓世家，长沙老九门狗五爷之孙，南派土夫子巨头吴三省的侄子，吴家现任当家。</dd>
  <dt>小哥</dt>
  <dd>张起灵，南派三叔作品《盗墓笔记》里面的灵魂人物，本书主角之一，铁三角的精神领袖。一出现就给所有人安全感与希望，几乎无所不能。</dd>
  <dt>胖子</dt>
  <dd>王胖子，南派三叔作品《盗墓笔记》中的人物，与吴邪、张起灵合称为“铁三角”。与二人相遇前似乎一直单干。自称是北派摸金校尉以及倒斗界肥王子，身手不凡，胆大心细，平时看着大大咧咧不靠谱，实际比较有分寸，关键时刻不掉链子。</dd>
</dl>
```

## 嵌套列表
在单独项下添加嵌套列表
```html
<ol>
  <li>
    <p>牛奶<p>
    <ul>
      <li>伊利</li>
      <li>蒙牛</li>
    </ul>
  </li>
  <li>鸡蛋</li>
  <li>面包</li>
  <li>火腿</li>
</ol>
```

# 重点强调

## 强调
表示讽刺等，一些特殊意义。使用`em`标签

```html
<p>你真是一个<em>好</em>人啊</p>
```

## 重要
使用`strong`标签来表示着重一句话中的某些字。
```html
<p>我真的好喜欢你，<strong>嫁给我吧！</strong></p>
```

# 引用
## 块引用
块引用使用`blockquote`，默认会有一个缩进，在cite属性内标明被引用的资源，使用cite标签来包裹引用的文字，然后使用a标签来包裹cite，并连接向被引用的资源
```html
<blockquote cite="https://baike.baidu.com/item/%E6%88%98%E7%95%A5%E4%B8%8A%E8%97%90%E8%A7%86%E6%95%8C%E4%BA%BA%EF%BC%8C%E6%88%98%E6%9C%AF%E4%B8%8A%E9%87%8D%E8%A7%86%E6%95%8C%E4%BA%BA/3800588">
    <a href="https://baike.baidu.com/item/%E6%88%98%E7%95%A5%E4%B8%8A%E8%97%90%E8%A7%86%E6%95%8C%E4%BA%BA%EF%BC%8C%E6%88%98%E6%9C%AF%E4%B8%8A%E9%87%8D%E8%A7%86%E6%95%8C%E4%BA%BA/3800588">
      <cite>毛主席说过：在战略上藐视敌人，在战术上重视敌人</cite>
    </a>
</blockquote>
```
## 行内引用
块引用使用`q`,默认会有双引号包裹文字，在cite属性内标明被引用的资源，使用cite标签来包裹引用的文字，然后使用a标签来包裹cite，并连接向被引用的资源
```html
<p>
  <q cite="https://baike.baidu.com/item/%E6%88%98%E7%95%A5%E4%B8%8A%E8%97%90%E8%A7%86%E6%95%8C%E4%BA%BA%EF%BC%8C%E6%88%98%E6%9C%AF%E4%B8%8A%E9%87%8D%E8%A7%86%E6%95%8C%E4%BA%BA/3800588">
    <a href="https://baike.baidu.com/item/%E6%88%98%E7%95%A5%E4%B8%8A%E8%97%90%E8%A7%86%E6%95%8C%E4%BA%BA%EF%BC%8C%E6%88%98%E6%9C%AF%E4%B8%8A%E9%87%8D%E8%A7%86%E6%95%8C%E4%BA%BA/3800588">
      <cite>毛主席说过：在战略上藐视敌人，在战术上重视敌人</cite>
    </a>
  </q>
</p>
```

# 缩略语
`abbr`来表示一个缩略语，全拼放在title属性内。鼠标hover效果时会出现title内全拼
```html
<p>
  <abbr title="Xi'an International University">XAIU</abbr>
</p>
```

# 标记联系方式
`address`来表示一个联系方式
```html
<address>
  <p>中国，西安，雁塔区，南窑头国际</p>
</address>
```

# 上下标
`sup`表示上标，`sub`表示下标

例如，x的平方，和水的化学分子式。
```html
<p>x<sup>2</sup></p>
<p>H<sub>2</sub>O</p>
```

# 日期和时间
`datetime`属性表示此元素的时间和日期，并且属性值必须是一个有效的日期格式
```html
<time datetime="2018-10-21">2018年10月21日</time>
```

# 展示计算机代码
- `code`用于标记计算机通用代码。
- `pre`对保留的空格（通常是代码块）——如果您在文本中使用缩进或多余的空白，浏览器将忽略它，您将不会在呈现的页面上看到它。但是，如果您将文本包含在`pre`标签中，那么空白将会以与你在文本编辑器中看到的相同的方式渲染出来。
- `var`用于标记具体变量名。
- `kbd`用于标记输入电脑的键盘（或其他类型）输入。
- `samp`用于标记计算机程序的输出。

例子:
```html
<pre>
  <code>
var a = 1;
console.log(a);
  </code>
</pre>
<p>
  请问变量 <var>a</var> 是多少
</p>
<p>
  变量 <var>a</var> 是 <samp>1</samp>
</p>
```

# html5重新定义的语义
- `i`被用来传达传统上用斜体表达的意义：外国文字，分类名称，技术术语，一种思想……
- `b`被用来传达传统上用粗体表达的意义：关键字，产品名称，引导句……
- `u`被用来传达传统上用下划线表达的意义：专有名词，拼写错误……

