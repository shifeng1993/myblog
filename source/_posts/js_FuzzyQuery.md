---
title: 【js】模糊查询算法
categories: JS
date: 2017-08-28
tags: 
  - JS
  - 算法
---

最近，写vue的时候，产品大佬提了个要求，要求做前端的模糊查询。二话不说，原生撸起来。

# 模糊查询的思路
参考传统c的二分递归算法，
循环遍历维度中，给二分法传递匹配的字符以及整个字符串，再利用返回的数组下标来确定是否匹配，遍历完成，输出数组。

## 需要数据
- 一个原始的表格（两个维度，元组和属性）
- 需要查询的字符串，
- 需要进行比对匹配的字段名称。

# 代码

## 首先是核心算法：二分递归算法
实现思路：每一次查找与中间值比较，可以确定是否查找成功，不成功当前查找区间缩小一半，递归找，即可

```javascript
const basicSearch = (mainStr, str, offset) => {
  let _this = this
  let mainLength = mainStr.length;
  let searchLength = str.length;
  if (searchLength > mainLength - offset) {
    return -1;
  }
  offset = offset || 0;
  for (let i = 0; i < str.length; i++) {
    if (str.charAt(i) !== mainStr.charAt(offset + i)) {
      return _this.basicSearch(mainStr, str, offset + 1)
    }
  }
  return offset;
}
```

## 其次是序列化函数
```javascript
const models = [{name: 'foo', age: 2},{name: 'bar', age: 4}];
const matchs = ['name', 'age']

const showList = (str) => {
  let _this = this;
  if (str !== '') {
    let arr = []
    // 按照两个维度查询，第一个维度循环 list。第二个维度循环mainstr
    for (let i = 0; i < _this.list.length; i++) {
      if (_this.matchs !== undefined) {
        for (let j = 0; j < _this.matchs.length; j++) {
          let queryMainStr = _this.list[i][_this.matchs[j]]
          if (queryMainStr !== null) {
            // 先全部转换成小写字母
            let mainStr = queryMainStr.toLowerCase()
            str = str.toLowerCase()
            // 获取二分法返回的下标
            let offset = _this.basicSearch(mainStr, str)
            if (offset !== -1) {
              if (arr.indexOf(_this.list[i]) === -1) {
                  arr.push(_this.list[i]);
              }
              // 倒序
              arr.reverse()
            }
          }
        }
      } else {
        let mainStr = _this.list[i].toLowerCase()
        str = str.toLowerCase()
        let offset = _this.basicSearch(mainStr, str)
        if (offset !== -1) {
          arr.push(_this.list[i])
          arr.reverse()
        }
      }
    }
    return arr;
  } else {
    return models;
  }
}
// 运行函数 并打印
console.log(showList('foo'))
```