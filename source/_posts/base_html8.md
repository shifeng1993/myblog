---
title: 【基础学习-html】8.html表单校验
categories: base
date: 2018-10-27 12:02:00
tags:
  - base
  - html
---
前端表单校验分为两种，一种是内置表单校验，一种是js表单校验。
校验的目的：
- 希望数据格式正确
- 保护用户数据
- 保护应用和服务器

# 内置校验（基础）

## 必填项
使用 `required`属性进行校验
```html
<form>
  <label for="choose">你吵架时候偏袒你媳妇还是你妈?</label>
  <input id="choose" name="i_like" required>
  <button>提交</button>
</form>
```
使用css伪类 `:invalid` 来进行列表的样式化
```css
input:invalid {
  box-shadow: 0 0 5px 1px red;
}

input:focus:invalid {
  outline: none;
}
```

## 规定长度
字符串类型输入使用 `minlength` 和 `maxlength`限制长度
数字类型输入使用 `min` 和 `max`限制长度
```html
<form>
  <div>
    <label for="choose">随便输入六个字?</label>
    <input id="choose" name="i_like" required minlength="6" maxlength="6">
  </div>
  <div>
    <label for="number">输入你想吃的巧克力数量?</label>
    <input type="number" id="number" name="amount" value="1" min="1" max="10">
  </div>
  <div>
    <button>Submit</button>
  </div>
</form>
```
同样，使用css伪类 `:invalid` 来进行列表的样式化
```css
input:invalid {
  box-shadow: 0 0 5px 1px red;
}

input:focus:invalid {
  outline: none;
}
```

## 使用正则校验
使用`pattern`属性加入正则，
例如： 枚举，我妈和我媳妇
```html
<form>
  <label for="choose">你吵架时候偏袒你媳妇还是你妈?</label>
  <input id="choose" name="i_like" required pattern="我妈|我媳妇">
  <button>提交</button>
</form>
```
除了以上例子之外还有很多常用正则。
### 邮件
```
^\w+([-+.]\w+)*@\w+([-.]\w+)*\.\w+([-.]\w+)*$
```
### 手机号
```
^(13[0-9]|14[5|7]|15[0|1|2|3|5|6|7|8|9]|18[0|1|2|3|5|6|7|8|9])\d{8}$
```
### 身份证号
15位、18位数字
```
^\d{15}|\d{18}$
```
### 账号是否合法
字母开头，允许5-16字节，允许字母数字下划线
```
^[a-zA-Z][a-zA-Z0-9_]{4,15}$
```
### 密码
以字母开头，长度在6~18之间，只能包含字母、数字和下划线
```
^[a-zA-Z]\w{5,17}$
```
### 强密码
必须包含大小写字母和数字的组合，不能使用特殊字符，长度在8-10之间
```
^(?=.*\d)(?=.*[a-z])(?=.*[A-Z]).{8,10}$
```
### 域名
```
[a-zA-Z0-9][-a-zA-Z0-9]{0,62}(/.[a-zA-Z0-9][-a-zA-Z0-9]{0,62})+/.?
```
### 日期格式
2018-10-27
```
^[1-9]\d{3}-(0[1-9]|1[0-2])-(0[1-9]|[1-2][0-9]|3[0-1])$
```
### 时间格式
12:00:00
```
^(20|21|22|23|[0-1]\d):[0-5]\d:[0-5]\d$
```

# js校验（高级和扩展）
## 使用H5的约束校验API（constraint validation）
想样式统一浏览器，和优化用户体验，则必须使用js 校验

### 使用
#### html
首先给form元素增加 novalidate 属性，关闭浏览器内置验证
```html
<form novalidate>
  <p>
    <label for="mail">
      <span>请输入邮箱地址:</span>
      <input type="email" id="mail" name="mail">
      <span class="error" aria-live="polite"></span>
    </label>
  </p>
  <button>Submit</button>
</form>
```
#### css
css 添加校验周期的伪类样式， `:valid、:invalid、:in-range 、:out-of-range`
```css
body {
  font: 1em sans-serif;
  padding: 0;
  margin : 0;
}

form {
  max-width: 200px;
}

p * {
  display: block;
}

input[type=email]{
  -webkit-appearance: none;

  width: 100%;
  border: 1px solid #333;
  margin: 0;

  font-family: inherit;
  font-size: 90%;

  -moz-box-sizing: border-box;
  box-sizing: border-box;
}

/* 验证失败的元素样式 */
input:invalid{
  border-color: #900;
  background-color: #FDD;
}

input:focus:invalid {
  outline: none;
}

/* 错误消息的样式 */
.error {
  width  : 100%;
  padding: 0;
 
  font-size: 80%;
  color: white;
  background-color: #900;
  border-radius: 0 0 5px 5px;
 
  -moz-box-sizing: border-box;
  box-sizing: border-box;
}

.error.active {
  padding: 0.3em;
}
```
#### js
```javascript

var form  = document.getElementsByTagName('form')[0];
var email = document.getElementById('mail');
var error = document.querySelector('.error');

email.addEventListener("input", function (event) {
  // 当用户输入信息时，验证 email 字段
  if (email.validity.valid) {
    // 如果验证通过，清除已显示的错误消息
    error.innerHTML = ""; // 重置消息的内容
    error.className = "error"; // 重置消息的显示状态
  }
}, false);
form.addEventListener("submit", function (event) {
  // 当用户提交表单时，验证 email 字段
  if (!email.validity.valid) {
    
    // 如果验证失败，显示一个自定义错误
    error.innerHTML = "I expect an e-mail, darling!";
    error.className = "error active";
    // 还需要阻止表单提交事件，以取消数据传送
    event.preventDefault();
  }
}, false);
```

## 不使用约束校验api（constraint validation）
一般为了兼容不支持 h5校验api 的老式浏览器，例如ie6,
#### html
```html
<form>
  <p>
    <label for="mail">
        <span>请输入邮箱地址:</span>
        <input type="text" class="mail" id="mail" name="mail">
        <span class="error" aria-live="polite"></span>
    </label>
  <p>
  <!-- 老式浏览器 需要增加type submit -->
  <button type="submit">Submit</button>
</form>
```
#### css
```css
body {
  font: 1em sans-serif;
  padding: 0;
  margin : 0;
}

form {
  max-width: 200px;
}

p * {
  display: block;
}

input.mail {
  -webkit-appearance: none;

  width: 100%;
  border: 1px solid #333;
  margin: 0;

  font-family: inherit;
  font-size: 90%;

  -moz-box-sizing: border-box;
  box-sizing: border-box;
}

/* 验证失败的元素样式 */
input.invalid{
  border-color: #900;
  background-color: #FDD;
}

input:focus.invalid {
  outline: none;
}

/* 错误消息的样式 */
.error {
  width  : 100%;
  padding: 0;
 
  font-size: 80%;
  color: white;
  background-color: #900;
  border-radius: 0 0 5px 5px;
 
  -moz-box-sizing: border-box;
  box-sizing: border-box;
}

.error.active {
  padding: 0.3em;
}
```
#### js
```javascript
// 使用旧版浏览器选择 DOM 节点的方法较少
var form  = document.getElementsByTagName('form')[0];
var email = document.getElementById('mail');

// 以下是在 DOM 中访问下一个兄弟元素的技巧
// 这比较危险，很容易引起无限循环
// 在现代浏览器中，应该使用 element.nextElementSibling
var error = email;
while ((error = error.nextSibling).nodeType != 1);

// 按照 HTML5 规范
var emailRegExp = /^[a-zA-Z0-9.!#$%&'*+/=?^_`{|}~-]+@[a-zA-Z0-9-]+(?:\.[a-zA-Z0-9-]+)*$/;

// 许多旧版浏览器不支持 addEventListener 方法
// 这只是其中一种简单的处理方法
function addEvent(element, event, callback) {
  var previousEventCallBack = element["on"+event];
  element["on"+event] = function (e) {
    var output = callback(e);
    
    // 返回 `false` 来停止回调链，并中断事件的执行
    if (output === false) return false;

    if (typeof previousEventCallBack === 'function') {
      output = previousEventCallBack(e);
      if(output === false) return false;
    }
  }
};

// 现在我们可以重构字段的验证约束了
// 由于不使用 CSS 伪类, 我们必须明确地设置 valid 或 invalid 类到 email 字段上
addEvent(window, "load", function () {
  // 在这里验证字段是否为空（请记住，该字段不是必需的）
  // 如果非空，检查它的内容格式是不是合格的 e-mail 地址
  var test = email.value.length === 0 || emailRegExp.test(email.value);
 
  email.className = test ? "valid" : "invalid";
});

// 处理用户输入事件
addEvent(email, "input", function () {
  var test = email.value.length === 0 || emailRegExp.test(email.value);
  if (test) {
    email.className = "valid";
    error.innerHTML = "";
    error.className = "error";
  } else {
    email.className = "invalid";
  }
});

// 处理表单提交事件
addEvent(form, "submit", function () {
  var test = email.value.length === 0 || emailRegExp.test(email.value);
 
  if (!test) {
    email.className = "invalid";
    error.innerHTML = "I expect an e-mail, darling!";
    error.className = "error active";

    // 某些旧版浏览器不支持 event.preventDefault() 方法
    return false;
  } else {
    email.className = "valid";
    error.innerHTML = "";
    error.className = "error";
  }
});
```

# 总结
为了避免重复造轮子和一些兼容问题，可以使用一些库来作为实际项目中的校验。
- 原生js
  - [Validate.js](http://rickharrison.github.io/validate.js/)
- jquery
  - [Validation](http://bassistance.de/jquery-plugins/jquery-plugin-validation/)
  - [Valid8](http://unwrongest.com/projects/valid8/)