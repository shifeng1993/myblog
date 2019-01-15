---
title: 【查漏-js】6.面向对象创建对象的模式
date: 2019-01-15 19:07:00
tags:
  - base
  - js
---
面向对象基础模式分为三种：
- 工厂模式
- 构造函数模式
- 原型模式

但是可以相互组合和运用：
- 组合使用构造函数模式和原型模式
- 动态原型模式
- 寄生构造函数模式
- 稳妥构造函数模式

# 工厂模式
工厂模式是做一个工厂函数，内部创建一个对象用来接受实参的值，然后赋值在对应的属性上，最后把这个对象return出去。

示例：
```javascript
function createGirl(name, age, job) {
  var girl = new Object();
  girl.name = name;
  girl.age = age;
  girl.job = job;
  girl.sayName = function () {
    console.log(this.name);
  };
  return girl;
}
var girl1 = createGirl("Lily", 27, "医生");
var girl2 = createGirl("Arutoria", 18, "护士");

console.log(girl1); // { name: 'Lily', age: 27, job: '医生', sayName: [Function] }
console.log(girl2); // { name: 'Arutoria', age: 18, job: '护士', sayName: [Function] }
```

# 构造函数模式
构造函数在经过 new 后会执行以下步骤：
1. 创建一个新对象, 这个对象等于`Girl`函数的`prototype`;
2. 将构造函数的作用域赋给新对象(因此 this 就指向了这个新对象);
3. 执行构造函数中的代码(为这个新对象添加属性);
4. 返回new后的新对象。对象的`__proto__`等于new之前构造函数的`prototype`;

```javascript
function Girl(name, age, job) {
  this.name = name;
  this.age = age;
  this.job = job;
  this.sayName = function () {
    console.log(this.name);
  };
}
var girl1 = new Girl("Lily", 27, "医生");
var girl2 = new Girl("Arutoria", 18, "护士");

console.log(girl1); // Girl { name: 'Lily', age: 27, job: '医生', sayName: [Function] }
console.log(girl2); // Girl { name: 'Arutoria', age: 18, job: '护士', sayName: [Function] }
console.log(girl2.__proto__); // Girl {}
console.log(Girl.prototype); // Girl {}
```

# 原型模式
