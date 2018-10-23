---
title: 【基础学习-html】5.html表格
categories: base
date: 2018-10-23 22:33:00
tags:
  - base
  - html
---
# 新建表格
创建`table`元素

```html
<table></table>
```

# 创建一行
使用`tr`元素（table row）

```html
<table>
  <tr></tr>
</table>
```

# 单元格
使用`td`元素（table data）

```html
<table>
  <tr>
    <td>Lily</td>
    <td>15</td>
    <td>女</td>
  </tr>
</table>
```

# 表头
使用`th`元素（table head）,会自动加粗居中
```html
<table>
  <tr>
    <th>name</th>
    <th>age</th>
    <th>gender</th>
  </tr>
  <tr>
    <td>Lily</td>
    <td>15</td>
    <td>女</td>
  </tr>
</table>
```

# 标题
标题使用`caption`元素;

```html
<table>
  <caption>一年一班统计</caption>
  <tr>
    <th>name</th>
    <th>age</th>
    <th>gender</th>
  </tr>
  <tr>
    <td>Lily</td>
    <td>15</td>
    <td>女</td>
  </tr>
</table>
```

# 合并单元格
使用`colspan`和 `rowspan`属性，值是一个int类型

例如：合并第一列的两行
```html
<table>
  <caption>一年一班统计</caption>
  <tr>
    <th>class</th>
    <th>name</th>
    <th>age</th>
    <th>gender</th>
  </tr>
  <tr>
    <td rowspan="2">一年一班</td>
    <td>Lily</td>
    <td>15</td>
    <td>女</td>
  </tr>
  <tr>
    <td>Abby</td>
    <td>16</td>
    <td>女</td>
  </tr>
</table>
```

# 设置某一列的样式
使用`colgroup`容器包裹`col`元素

一般单独修改某一列，会写出等于列数 col数量，在单独设置样式

修改全部列，可以只使用一个col 并设置 `span=""`对应列数属性

```html
<table>
  <caption>一年一班统计</caption>
  <colgroup>
    <col>
    <col>
    <col style="background:blue">
    <col>
  </colgroup>
  <tr>
    <th>class</th>
    <th>name</th>
    <th>age</th>
    <th>gender</th>
  </tr>
  <tr>
    <td rowspan="2">一年一班</td>
    <td>Lily</td>
    <td>15</td>
    <td>女</td>
  </tr>
  <tr>
    <td>Abby</td>
    <td>16</td>
    <td>女</td>
  </tr>
</table>
```

# 结构化表格
添加`thead`, `tfoot`, 和 `tbody`
- thead 需要放在表格头部，但是如果使用了`colgroup`，需要放在`colgroup`下面
- tfoot 需要放在底部
- tbody 需要放在thead 和tfoot中间

```html
<table>
  <caption>一年一班统计</caption>
  <colgroup>
    <col>
    <col>
    <col style="background:blue">
    <col>
  </colgroup>
  <thead>
    <tr>
      <th>class</th>
      <th>name</th>
      <th>age</th>
      <th>gender</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td rowspan="2">一年一班</td>
      <td>Lily</td>
      <td>15</td>
      <td>女</td>
    </tr>
    <tr>
      <td>Abby</td>
      <td>16</td>
      <td>女</td>
    </tr>
  </tbody>
</table>
```

# scope属性
可以将数据单元格与表头单元格联系起来，不会产生视觉效果，是给视力障碍的人事使用
- col规定单元格是列的表头。
- row规定单元格是行的表头。
- colgroup规定单元格是列组的表头。
- rowgroup规定单元格是行组的表头。
