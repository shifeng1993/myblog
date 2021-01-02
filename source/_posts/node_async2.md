---
title: 【nodejs异步部分复习】2.发布订阅和观察者模式
date: 2021-01-02 16:22:00
tags:
  - base
  - nodejs
  - js
---

## 发布订阅
分为两部分
- on 将注册的函数维护到一个数组中
- emit 将数组中的方法依次执行

```js
let eventCenter = {
  arr: [], // 订阅和发布没有明显的关联，靠中介来实现
  on(fn) {
    fn && this.arr.push(fn);
  },
  emit() {
    this.arr.forEach(fn => fn());
  }
}


eventCenter.on(function () {
  console.log(1)
})
eventCenter.on(function () {
  console.log(2)
})
eventCenter.on(function () {
  console.log(3)
})
eventCenter.on(function () {
  console.log(4)
})


eventCenter.emit();

// 1
// 2
// 3
// 4
```


## 观察者模式(Observer)
观察和被观察之间有关联，观察者需要放在被观察者中，被观察者的状态发生变化需要通知观察者，

内部也是基于发布订阅模式，收集观察者，状态变化后需要通知观察者
```js
class Subject {
  constructor(name) {
    this.name = name;
    this.state = '';
    this.observers = [];
  }
  attach(o) {
    this.observers.push(o);
  }
  setState(newState) {
    this.state = newState;
    this.observers.forEach(o => o.update(this))
  }
}

class Observer {
  constructor(name) {
    this.name = name;
  }

  update(subject) {
    console.log(subject.state)
  }
}

let a = new Subject('a');
let b = new Observer('b');
let c = new Observer('c');

a.attach(b);
a.attach(c);
a.setState('dsadas')

```