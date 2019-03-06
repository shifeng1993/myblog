---
title: 【查漏-js】3.数组的归并方法reduce reduceRight
categories: base
date: 2019-01-11 00:13:00
tags:
  - base
  - js
---
数组的归并是es5增加的，所以ie9+和主流浏览器基本支持。

----------------------
# 原文
ECMAScript 5 还新增了两个归并数组的方法:reduce()和 reduceRight()。这两个方法都会迭代数组的所有项，然后构建一个最终返回的值。其中，reduce()方法从数组的第一项开始，逐个遍历 到最后。而 reduceRight()则从数组的最后一项开始，向前遍历到第一项。

这两个方法都接收两个参数: 

- 一个在每一项上调用的函数
- 归并基础的初始值(可选的)

传给 reduce()和 reduceRight()的函数接收 4 个参数:前一个值、当前值、项的索引和数组对象。

这个函数返回的任何值都会作为第一个参数自动传给下一项。

----------------------
# 理解和示例
```javascript
list.reduce((prev, cur, index, array) => {
  return prev + cur;
}, initValue);
```
迭代次数是按有没有reduce第二个参数 归并前的初始值算的（initValue）；
- 如果有初始值，则迭代数组的length次，
- 如果没初始值，则迭代数组的 length - 1 次

函数参数 `prev` 是归并的和。`cur`是当前项，`index`是当前项下标，`array`是原数组

迭代开始后，第一项第二项计算的值会作为参数传给下次迭代的prev

## 简单累加
```javascript
let list = [0, 2, 2, 3, 4, 5, 6, 7, 8, 9];

let newArr = list.reduce((prev, cur, index, array) => {
  return prev + cur;
});

console.log(newArr); // 46
```

## 数组扁平化
```javascript
let list = [[1, 2], [3, 4], [[5, 6, [7, 8, [9, 10]]]]];

const flatten = (arr) => arr.reduce((prev,cur) => prev.concat(Array.isArray(cur) ? flatten(cur) : cur), []);

console.log(flatten(list));// => [ 1, 2, 3, 4, 5, 6, 7, 8, 9, 10 ];
```

# 手动实现
```javascript
Array.prototype.reduce = function (reducer, initVal = null) {
  for (let i = 0; i < this.length; i++) {
    initVal = reducer(initVal, this[i], i, this);
  }
  return initVal;
}

let arr = [1, 2, 3, 4];

let newarr = arr.reduce((val, current, index, arr) => {
  let num = val += current;
  return num;
})

console.log(newarr); // 10
```