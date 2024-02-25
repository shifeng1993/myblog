---
title: 【js】获取url query参数的方法
categories: JS
date: 2017-08-06
tags:
  - JS
  - url参数
---
```javascript
const getUrlParams = (url) => {
    let str = url.split('?')[1]
    let items = str.split('&')
    let result = {};
    let arr = [];
    for (let i = 0; i < items.length; i++) {
        arr = items[i].split('=');
        result[arr[0]] = arr[1];
    }
    return result
}
```