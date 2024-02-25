---
title: 【查漏-js】5.面向对象之属性类型
date: 2019-01-15 17:07:00
tags:
  - base
  - js
---
面向对象的属性分为两种：
- 数据属性
- 访问器属性

# 数据属性  
- Configurable 表示能否通过 delete 删除属性从而重新定义属性，默认值为true。
- Enumerable 表示能否通过 for-in 循环返回属性，默认值为true。
- Writable 表示能否修改属性的值，默认值为true。
- Value 包含这个属性的数据值。

数据属性使用`Object.defineProperty`方法进行重写，如果没有重写则为默认值。

示例：
```javascript
var girl = {
  name: 'Lily'
}

Object.defineProperty(girl, "name", {
  configurable: false,
  writable: false,
});

girl.name = '321';
delete girl.name;

console.log(girl); // { name: 'Lily' }
```

# 访问器属性
- Configurable 表示能否通过 delete 删除属性从而重新定义属性，默认值为true。
- Enumerable 表示能否通过 for-in 循环返回属性，默认值为true。
- get 在读取属性时调用的函数，默认值为 undefined。
- set 在写入属性时调用的函数，默认值为 undefined。

访问器属性使用`Object.defineProperty`方法进行重写，如果没有重写则为默认值。

这里有个小坑：

一开始我是以为 age 定义了get和set方法后可以直接对`this.age`进行赋值操作，可是报一个堆栈溢出的错，最后看了一遍书发现理解错了。

可以用对象内私有属性来保存这个赋值 `this._age = value`，当然命名都是age是为了好记。

示例：
```javascript
var testObj = {
var girl = {
  name: 'Lily',
  age: 0
}

Object.defineProperty(girl, "age", {
  configurable: false, // 不允许删除
  get: function () {
    return this._age;
  },
  set: function (value) {
    if (value <= 18) {
      this._age = value;
    } else {
      this._age = 18; // 如果大于18岁，永远18
    }
  }
});

girl.age = 40;
console.log(girl.age); // 18
```

# 定义多个属性
`Object.defineProperty`是定义单个属性，定义多个属性用`Object.defineProperties`;

示例:
```javascript
var girl = {
  name: 'Lily',
  age: 0
}

Object.defineProperties(girl, {
  name: {
    configurable: false,
  },
  age: {
    configurable: false, // 不允许删除
    get: function () {
      return this._age;
    },
    set: function (value) {
      if (value <= 18) {
        this._age = value;
      } else {
        this._age = 18; // 如果大于18岁，永远18
      }
    }
  }
});

delete girl.name;
girl.age = 40;

console.log(girl.name); // Lily
console.log(girl.age); // 18
```

# 读取属性
使用`Object.getOwnPropertyDescriptor`方法。

示例：
```javascript
var girl = {
  name: 'Lily',
  age: 0
}

Object.defineProperties(girl, {
  name: {
    configurable: false,
  },
  age: {
    configurable: false, // 不允许删除
    get: function () {
      return this._age;
    },
    set: function (value) {
      if (value <= 18) {
        this._age = value;
      } else {
        this._age = 18; // 如果大于18岁，永远18
      }
    }
  }
});

delete girl.name;
girl.age = 40;

console.log(girl.name); // Lily
console.log(girl.age); // 18

var name = Object.getOwnPropertyDescriptor(girl, "name");
console.log(name.value); // Lily
console.log(name.configurable); // false
```
