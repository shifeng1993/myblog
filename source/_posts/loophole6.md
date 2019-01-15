---
title: 【查漏-js】6.面向对象创建对象的模式
date: 2019-01-15 19:07:00
tags:
  - base
  - js
---
# 基础模式
面向对象基础模式分为三种：
- 工厂模式
- 构造函数模式
- 原型模式

## 工厂模式
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

## 构造函数模式
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

## 原型模式
每一个函数原型都是一个对象，这个对象的用途是包含可以由特定类型的所有实例共享的属性和方法。

- new 后对象的`__proto__`是原型
- new 前函数的原型 是对象。
- 函数原型的构造函数是它本身。
- 函数原型上的`isPrototypeOf`方法可以判断，表示new出对象是否在另一个对象的原型链上。
- 对象的静态方法`getPrototypeOf`可以返回对象的原型。类似java的super。

示例：
```javascript
function Girl() {}

Girl.prototype.name = "Arutoria";
Girl.prototype.age = 18;
Girl.prototype.job = "护士";

var girl = new Girl();

console.log(girl); // Girl {}
console.log(girl.name); // Arutoria
console.log(girl.age); // 18
console.log(girl.job); // 护士
console.log(girl.__proto__); // Girl { name: 'Arutoria', age: 18, job: '护士' }
console.log(Girl.prototype); // Girl { name: 'Arutoria', age: 18, job: '护士' }
console.log(Girl.prototype.constructor); // [Function: Girl]
console.log(Girl.prototype.isPrototypeOf(girl)); // true
console.log(Object.getPrototypeOf(girl)); // Girl { name: 'Arutoria', age: 18, job: '护士' }
console.log(Object.getPrototypeOf(girl) === Girl.prototype) // true

```

# 结合与使用
- 组合使用构造函数模式和原型模式
- 动态原型模式
- 寄生构造函数模式
- 稳妥构造函数模式

## 组合使用构造函数模式和原型模式
这种模式的优点是每个实例都会有自己的一份实例属性的副本， 但同时又共享着对方法的引用，最大限度地节省了内存。

示例：
```javascript
function Girl(name, age, job) {
  name && (this.name = name);
  age && (this.age = age);
  job && (this.job = job);
}

Girl.prototype.name = "未知";
Girl.prototype.age = 0;
Girl.prototype.job = "无";

var girl1 = new Girl('Arutoria', 11);
var girl2 = new Girl('Amia', null, 'doctor');
var girl3 = new Girl('Lafite');

console.log(girl1.name, girl1.age, girl1.job); // Arutoria 11 无

console.log(girl2.name, girl2.age, girl2.job); // Amia 0 doctor

console.log(girl3.name, girl3.age, girl3.job); // Lafite 0 无
```

## 动态原型模式
动态原型模式就是给是否添加原型做判断，判断的依据为构造函数的实参内是否有对应的参数。

示例:
```javascript
function Girl(name, age, job) {
  name && (this.name = name);
  age && (this.age = age);
  job && (this.job = job);
}

if (typeof this.name !== "string") {
  Girl.prototype.name = "未知";
}
if (typeof this.name !== "string") {
  Girl.prototype.age = 0;
}
if (typeof this.name !== "string") {
  Girl.prototype.job = "无";
}

var girl1 = new Girl('Arutoria', 11);
var girl2 = new Girl('Amia', null, 'doctor');
var girl3 = new Girl('Lafite');

console.log(girl1.name, girl1.age, girl1.job); // Arutoria 11 无

console.log(girl2.name, girl2.age, girl2.job); // Amia 0 doctor

console.log(girl3.name, girl3.age, girl3.job); // Lafite 0 无
```

## 寄生构造函数模式
这个构造模式的，构造函数在不返回值的情况下，默认会返回新对象实例。

优点是可以通过在构造函数的末尾添加一个 return 语句，可以重写调用构造函数时返回的值。

注意，跟工厂模式非常像，但是不是工厂模式。

示例：
```javascript
function Girl(name, age, job) {
  var girl = new Object();
  girl.name = name;
  girl.age = age;
  girl.job = job;
  girl.sayName = function () {
    console.log(this.name);
  };
  return {
    a: 1
  };
}
var girl1 = new Girl("Lily", 27, "医生");
var girl2 = new Girl("Arutoria", 18, "护士");

console.log(girl1); // { a: 1 }
console.log(girl2); // { a: 1 }
```

## 稳妥构造函数模式
书里面写，稳妥对象最适合在 一些安全的环境中(这些环境中会禁止使用 this 和 new)，或者在防止数据被其他应用程序(如 Mashup 程序)改动时使用。

通俗一点讲，其实就是模仿java类内 `private变量` 不能被直接修改，要通过`get函数`读取，`set函数`写入。

以下是示例：
```javascript
function createGirl() {
  var name = arguments[0];
  var age = arguments[1];
  var job = arguments[2];
  var girl = new Object();
  girl.getName = function () {
    return name;
  };
  return girl;
}

var girl1 = createGirl("Arutoria", 18, "护士");

console.log(girl1.getName()); // Arutoria
console.log(girl1.name); // undefined
```