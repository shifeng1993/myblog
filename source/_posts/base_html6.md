---
title: 【基础学习-html】6.html表单基础
categories: base
date: 2018-10-24 20:42:00
tags:
  - base
  - html
---
# form
form 元素用来包裹一个表单，一般不能嵌套使用。

# 通用属性
- autofocus 自动获取焦点，默认不获取。
- disabled 禁用元素，不允许表单元素进行交互
- name  元素的名称，用于表单数据提交时候的键名
- value 元素的值，可以用来进行数据绑定


# fieldset 和 legend
在form的表单元素创建一个块，用来标注一块结构。
```html
<form>
  <fieldset>
    <legend>Fruit juice size</legend>
    <p>
      <input type="radio" name="size" id="size_1" value="small">
      <label for="size_1">Small</label>
    </p>
    <p>
      <input type="radio" name="size" id="size_2" value="medium">
      <label for="size_2">Medium</label>
    </p>
    <p>
      <input type="radio" name="size" id="size_3" value="large">
      <label for="size_3">Large</label>
    </p>
  </fieldset>
</form>
```

# 文本域
文本域有一些通用特点
- 可以被标记为 readonly 和 disabled
- 都有 placeholder
- 可以限制在输入框范围内，可以限制最大字符数

## 单行文本域
默认 type为text
```html
<input type="text" id="text1" name="text1" value="我是单行文本域">
```

## email地址域
设置input属性type 为 email，multiple属性是可以用逗号分隔的形式书写多个email地址

移动端会出现适用于email格式的专属的虚拟键盘
```html
<input type="email" id="email" name="email" multiple>
```

## 密码域
设置input属性type 为 password, 密码域会被小点或星星代替字符，需要注意的是不要明文发送真实密码
```html
<input type="password" id="pwd" name="pwd">
```

## 搜索域
设置input属性type 为 search, 搜索字段以圆角和/或给定一个“x”来清除输入的值,value可以自动保存到同一站点上的多个页面上自动完成
```html
<input type="search" id="search" name="search">
```

## 电话号码域
设置input属性type 为 tel,值没有类型限制，但是移动端会出现一个适合输入电话号码的键盘。
```html
<input type="tel" id="tel" name="tel">
```

## url 域
设置input属性type 为 url,值如果输入无效的url，浏览器就会报告错误
```html
<input type="url" id="url" name="url">
```

# 多行文本域
使用textarea元素，多行和单行的区别，是多行支持用户回车换行。

属性：
- cols，默认20，文本控件的可见宽度，平均字符宽度。
- rows，控制的可见文本行数。
- wrap，默认soft，表示控件是如何包装文本的。可能的值：hard 或 soft

```html
<textarea cols="30" rows="10"></textarea>
```

# 下拉框
## 下拉单选框
单选框是使用select元素，selected属性表示已选
### 直接写
```html
<select id="simple" name="simple">
  <option>牛奶</option>
  <option>鸡蛋</option>
  <option>面包</option>
</select>
```
### 分组
```html
<select id="simple" name="simple">
   <optgroup label="牛奶">
    <option>蒙牛</option>
    <option>伊利</option>
  </optgroup>
  <option>鸡蛋</option>
  <option>面包</option>
</select>
```

## 下拉复选框
给select元素添加`multiple`属性 ,默认点击是单选，但是按住`cmd/ctrl`点击会是多选
```html
<select id="simple" name="simple" multiple>
   <optgroup label="牛奶">
    <option>蒙牛</option>
    <option>伊利</option>
  </optgroup>
  <option>鸡蛋</option>
  <option>面包</option>
</select>
```

# 单选框
设置input属性type 为 radio 可以创建单选框,注意只要键名name属性一致，就可以实现单选
```html
<fieldset>
  <legend>单选题</legend>
  <input type="radio" checked id="radio1" name="bar" value="1">
  <label for="radio1">a</label>
  <input type="radio" id="radio2" name="bar" value="2">
  <label for="radio2">b</label>
  <input type="radio" id="radio3" name="bar" value="3">
  <label for="radio3">c</label>
</fieldset>
```
# 复选框
设置input属性type 为 checkbox 可以创建复选框
```html
<fieldset>
  <legend>多选题</legend>
  <input type="checkbox" checked id="checkbox1" name="foo" value="1">
  <label for="checkbox1">a</label>
  <input type="checkbox" id="checkbox2" name="foo" value="2">
  <label for="checkbox2">b</label>
  <input type="checkbox" checked id="checkbox3" name="foo" value="3">
  <label for="checkbox3">c</label>
</fieldset>
```

# 按钮
input和button的区别是 input只能放文本，button可以放其他元素

## submit
将表单数据发送到服务器。
```html
<button type="submit">
    提交
</button>

<input type="submit" value="提交">
```

## reset
将所有表单小部件重新设置为它们的默认值。
```html
<button type="submit">
    重置
</button>

<input type="submit" value="重置">
```

## button
如果没有设置type 默认值为button
```html
<button type="button">
    添加
</button>

<input type="button" value="添加">
```

# label
可以用 label元素 的 for 属性和 input元素 或者 button等 表单元素的id进行关联.
```html
<form>
    <p>
      <input type="text" name="name" id="name" value="Lily">
      <label for="name">Small</label>
    </p>
</form>
```

# 发送数据
```html
<form action="http://localhost:3333/user" method="GET">
  <p>
    <label for="name">Small</label>
    <input type="text" name="name" id="name" value="Lily">
  </p>
  <p>
    <button>提交</button>
  </p>
</form>
```

## action
form元素的 action 属性可以用来发送数据给后端，例如发送到本机的3000端口

## method
form元素的 method 属性可以用来规定请求的方法。例如POST 和 GET

