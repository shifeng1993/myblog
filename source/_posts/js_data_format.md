---
title: 【js】时间格式化
categories: JS
date: 2017-08-18
tags: 
  - JS
  - 常用函数
---
库难用，自己撸一个。
参数，date， Date格式，

```javascript
getDateTime(date) {
  if (date.getFullYear() === null) {
    return null
  } else {
    let str = '';
    str += date.getFullYear() + '-';
    str += ((date.getMonth() + 1) < 10 ? '0' + (date.getMonth() + 1) : date.getMonth() + 1) + '-';
    str += (date.getDate() < 10 ? '0' + date.getDate() : date.getDate()) + ' ';
    str += (date.getHours() < 10 ? '0' + date.getHours() : date.getHours()) + ':';
    str += (date.getMinutes() < 10 ? '0' + date.getMinutes() : date.getMinutes()) + ':';
    str += (date.getSeconds() < 10 ? '0' + date.getSeconds() : date.getSeconds());
    return str;
  }
}
```