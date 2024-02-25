---
title: 【基础学习-html】7.html表单原生高级控件
categories: base
date: 2018-10-26 22:02:00
tags:
  - base
  - html
---
昨天25号有事忙了，今天先更一篇，明天刚好周六，多更一篇把昨天的补上
# 数字
type 为 number
- `min`最小值
- `max`最大值
- `step`增加或减少的步长

```html
<input type="number" name="age" id="age" min="1" max="10" step="2">
```

# 滑块
type 为 range
- `min`最小值
- `max`最大值
- `step`增加或减少的步长

ie10以下，不支持range。

```html
<input type="range" name="beans" id="beans" min="0" max="500" step="10">
```

# 日期时间选择器
使用原生日期时间选择器最大的问题是兼容，ie，ff，Safari都不支持
## 本地时间
```html
<input type="datetime-local" name="datetime" id="datetime">
```
## 月
```html
<input type="month" name="month" id="month">
```
## 时间
```html
<input type="time" name="time" id="time">
```
## 星期
```html
<input type="week" name="week" id="week">
```

# 颜色选择器
使用原生颜色选择器最大的问题是兼容，ie，Safari都不支持
```html
<input type="color" name="color" id="color">
```

# 文件选择器
使用multiple属性支持发送多个文件
```html
<input type="file" name="file" id="file" accept="image/*" multiple>
```

# 隐藏表单元素
```html
<input type="hidden" id="timestamp" name="timestamp" value="1286705410">
```

# 图像按钮
常用作网站的点击图片验证，属性基本与img标签一致， 点击后会以将点击图像的x，y轴位置发出 

例如:`http://foo.com?pos.x=123&pos.y=456`
```html
<input type="image" alt="Click me!" src="my-img.png" width="80" height="30" />
```

# 进度条
ie不支持
```html
<progress max="100" value="75">75/100</progress>
```

# 仪表
ie不支持
```html
<meter min="0" max="100" value="75" low="33" high="66" optimum="50">75</meter>
```