---
title: 【js】手动实现一个es6模板字符串
categories: JS
date: 2019-03-05 15:03:00
tags: 
  - JS
---

# es6模板字符串
首先写两个字符串，然后使用es6模板字符串试一下

```javascript
let obj = {
  name: 'lily',
  age: 90
}

let str1 = `${obj.name}的年龄是${obj.age}`;

console.log(str1); // lily的年龄是90
```

# 手动实现es6模板字符串
写一个普通字符串，内容和模板字符串一样

```javascript
let obj = {
  name: 'lily',
  age: 90
}

let str2 = '${obj.name}的年龄是${obj.age}';
```

创建一个命名函数`replacefunc`，用于匹配到对应的模板，并进行替换。

在`replacefunc`函数内创建一个正则`//`，对需要匹配的`${`和`}`进行匹配，并添加转译，在花括号中间添加分组`()`，对不能出现结束的花括号进行匹配`[^}]`，`^`为取反，并至少有一个字符`+`，添加全局匹配`g`。

后指定字符串方法`replace`的第二个函数参数。

match为正则匹配到的字符串，
key为分组匹配到的字符串，

循环两次是因为匹配到两次符合正则的字符串。

最后则用`eval()`还原，取到对应作用域内的变量。

```javascript
let obj = {
  name: 'lily',
  age: 90
}

let str2 = '${obj.name}的年龄是${obj.age}';

function replacefunc(desc) {
  return desc.replace(/\$\{([^}]+)\}/g, function (match, key) {
    console.log(match);  // ${obj.name} || ${obj.age}
    console.log(key); // obj.name || obj.age
    console.log(eval(key)); // lily || 90
    return eval(key);
  })
}
console.log(replacefunc(str2));
```

用途：可以在模板引擎中用到。