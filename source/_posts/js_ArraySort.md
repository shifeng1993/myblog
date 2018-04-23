---
title: 【js】风骚的数组排序算法
categories: JS
date: 2017-08-14
tags:
  - JS
  - 排序
---
有个js对象数组 var ary=[{id:1,name:"b"},{id:2,name:"b"}] 需求是根据name 或者 id的值来排序,这里有个风骚的函数

#### 函数定义
```javascript
function keysrt(key,desc) {
  return function(a,b){
    return desc ? ~~(a[key] < b[key]) : ~~(a[key] > b[key]);
  }
}
```
#### 代码使用
```javascript
var ary=[{id:1,name:"b"},{id:2,name:"b"}];
ary.sort(keysrt('name',true));
ary.sort(keysrt('name',false));
ary.sort(keysrt('id',false));
```
