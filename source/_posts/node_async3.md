---
title: 【nodejs异步部分复习】3.从0手写简易promise
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

## 简单的promise
1. promise 有三个状态，成功态，失败态，等待态
2. 成功失败是自定义的
3. promise默认执行器是立即执行
4. promise的实例都拥有一个then方法，有两个参数，一个是成功的回调，一个是失败的回调
5. 如果执行函数时，发生异常也会执行失败逻辑
6. 如果成功就不能失败，反之，如果失败，就不能成功，只有等待态，才能改变状态


### promise.js
```js
const FULFILLED = 'fulfilled';  // 成功态
const REJECTED = 'rejected';  // 失败态
const PENDING = 'pending';    // 等待态度

class Promise {
  // new时，传入一个执行器，并立即执行
  constructor(executer) {
    this.status = PENDING;  // 状态为等待态度
    this.value = undefined;   // 成功的原因
    this.reason = undefined;   // 失败的原因
    console.log('my promise')
    // 成功调用
    const resolve = (value) => {
      if (this.status === PENDING) {
        this.value = value;
        this.status = FULFILLED;
      }
    }
    // 失败调用
    const reject = (reason) => {
      if (this.status === PENDING) {
        this.reason = reason;
        this.status = REJECTED;
      }
    }

    try {
      executer(resolve, reject); // 传入的执行器立即执行
    } catch (error) { // 在执行器中加异常捕获，捕获之后直接走失败调用
      reject(error)
    }
  }

  /**
   * @description: 
   * @param {*} onFulfilled then内成功回调
   * @param {*} onRejected then内失败回调
   * @return {*}
   */
  then(onFulfilled, onRejected) {
    if (this.status === FULFILLED) {
      onFulfilled(this.value);
    }

    if (this.status === REJECTED) {
      onRejected(this.reason);
    }
  }
}

module.exports = Promise;

```

### index.js 

```js
const Promise = require('./promise.js');

let task = new Promise((resolve, reject) => {
  resolve(1)
  reject('报错了')
})
task.then(res => {
  console.log(res)
}, err => {

})
```