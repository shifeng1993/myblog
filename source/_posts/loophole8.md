---
title: 【查漏-js】8.闭包和模拟块级作用域
date: 2019-01-17 23:07:00
tags:
  - base
  - js
---
# 匿名函数（拉姆达函数）
匿名函数就是没有命名的函数，缺点是不会进行`函数声明提升`。

也就是说如果使用匿名函数给赋值变量。必须在赋值后再执行，赋值前执行会报错。

示例：
```javascript
foo(); // foo

// 命名函数foo
function foo() {
  console.log('foo');
}

foo(); // foo


bar(); // bar is not a function

// 匿名函数bar
var bar = function () {
  console.log('bar');
}

bar(); // bar
```



# 闭包
因为函数内 `var` 后变量作用域是函数内的。如果想在外部访问这个函数内的变量，就需要用到闭包。

创建闭包的常见方式，就是在一个函数内部套另一个函数。

闭包就类似面向对象里面的 稳妥构造函数模式。

示例：
```javascript
// 闭包示例
function girl() {
  var name = 'lily';
  var age = 18;

  function getName() {
    return name;
  }
  return getName;
}

console.log(girl()); // [Function: getName]
console.log(girl()()); // lily
```


# 闭包实现块级作用域
用一个立即执行的函数套一个匿名函数

```javascript
// 匿名函数创建块级作用域
(function () {
  var i = 1;
  console.log(i);
})(); // hello

console.log(i); // i is not defineds
```