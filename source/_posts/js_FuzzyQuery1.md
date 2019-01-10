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
  let mainLength = mainStr.length;
  let searchLength = str.length;
  if (searchLength > mainLength - offset) {
    return -1;
  }
  offset = offset || 0;
  for (let i = 0; i < str.length; i++) {
    if (str.charAt(i) !== mainStr.charAt(offset + i)) {
      return basicSearch(mainStr, str, offset + 1)
    }
  }
  return offset;
}
```

## 其次是序列化函数
```javascript
const models = [{name: 'foo', age: 2},{name: 'bar', age: 4}];
const matchs = ['name', 'age']

const multiQuery = ({str, dataArr = [], keyArr}) => {
  if (!Array.isArray(dataArr)) return dataArr    // 校验是数组
  if (!str || typeof str !== 'string') return dataArr;                     // 需要搜索的字符串是必须

  let arr = []
  dataArr.map((item1, index) => {
    if (!keyArr) {
      // 如果多字段不存在
      let mainStr = item1.toLowerCase()
      str = str.toLowerCase()
      let offset = basicSearch(mainStr, str)
      if (offset !== -1) {
        arr.push(item1)
        arr.reverse()
      }
    } else if (!!keyArr && Array.isArray(keyArr)) {
      // 如果多字段存在
      keyArr.map((item2, index) => {
        let queryMainStr;
        if (typeof item1[item2] === 'string') {
          queryMainStr = item1[item2]
        } else {
          queryMainStr = null
        }
        if (queryMainStr !== null) {
          // 先全部转换成小写字母
          let mainStr = queryMainStr.toLowerCase()
          str = str.toLowerCase()
          // 获取二分法返回的下标
          let offset = basicSearch(mainStr, str)
          if (offset !== -1) {
            if (arr.indexOf(item1) === -1) {
              arr.push(item1);
            }
            // 倒序
            arr.reverse()
          }
        }
      })
    } else {
      arr = [...dataArr];
    }
  })
  return arr;
}
// 运行函数 并打印
console.log(multiQuery({str: 'foo', dataArr: models, keyArr: matchs}))
```