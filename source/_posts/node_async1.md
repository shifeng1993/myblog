---
title: 【nodejs异步部分复习】1.高阶函数来解决异步
date: 2021-01-02 15:32:00
tags:
  - base
  - nodejs
  - js
---
> 高阶函数：参数是一个函数， 或者返回一个函数

## 应用场景

### before
```js
function say(...args){
  console.log('say',...args);
}

Function.prototype.before = function(callback){
  return (...args)=>{
    callback();
    this(...args);
  }
}

let beforeSay = say.before(function(){
console.log('before')
})

beforeSay('hello','world')
```

### 函数柯里化
判断变量的类型四种方法：
- typeof  缺点：不能判断引用类型，都是object
- constructor 可以找到这个变量是谁构造出来的 ({}).constructor
- instanceof 判断谁是谁的实例，原理通过__proto__上面有没有包含
- Object.prototype.toString.call() 缺点，不能细分谁是谁的实例，
  
```js
function isType(type){
  return function(value){
    return Object.prototype.toString.call(value) === `[object ${type}]`;
  }
}

let isArray = isType('Array');
isArray('1');
isArray([]);
```

- 实现一个通用的柯里化
```js
function isType(type, value) {
  console.log(type, value, Object.prototype.toString.call(value));
  return Object.prototype.toString.call(value) === `[object ${type}]`;
}

function currying(fn, arr = []) {
  let len = fn.length;

  return function (...args) {
    arr = [...arr, ...args];
    if (arr.length < len) {
      return currying(fn, arr); // 递归，不停的产生函数
    } else {
      return fn(...arr);
    }
  }
}


let isArray = currying(isType)('Array');
let isString = currying(isType)('String');

console.log(isArray([]));
console.log(isString('111'));
```

### after
```js
function after(times, callback) {
  return function () { // 闭包，定义的作用域和执行的作用域不在同一作用域下，就会产生一个闭包。
    if (--times === 0) {
      callback();
    }
  }
}

let cb = after(3, function () {
  console.log('完成');
})

cb();
cb();
cb();
```
