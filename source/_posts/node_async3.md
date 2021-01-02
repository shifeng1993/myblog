---
title: 【nodejs异步部分复习】3.从0手写promise
date: 2021-01-02 17:14:00
tags:
  - base
  - nodejs
  - js
---
> 基于promise A+规范文档实现，保证promise之间有相互的兼容性

[promiseA+ 规范文档，https://promisesaplus.com/](https://promisesaplus.com/)

兼容：
es6内部已经实现，ie全部不支持promise，需要polyfill来做兼容

## promise为什么会产生？
- 解决异步问题

1. 多个异步请求并发 Promise.all()
2. 链式异步请求，上个人的输出是下个人的输入，解决回调地狱
3. 缺点，还是基于回调函数

## 状态
promise 有三个状态
- 成功态
- 失败态
- 等待态

```js
let task = new Promise((resolve, reject) => {

})

```