---
title: 【js】模糊查询算法完善
categories: JS
date: 2019-01-10
tags: 
  - JS
  - 算法
---

最近，查漏补缺的时候，突然想到了前年写过的js模糊查询算法，于是就看能不能再完善一下。

# 模糊查询的思路
参考传统c的二分递归算法，
循环遍历维度中，给二分法传递匹配的字符以及整个字符串，再利用返回的数组下标来确定是否匹配，遍历完成，输出数组。

# 代码
这个次的算法改进，是为了适应json格式的数据。有时候表格不只是横竖两个维度。包括表格内套树形结构等。

下面是代码实现
## 首先是核心算法：二分法
实现思路：每一次查找与中间值比较，可以确定是否查找成功，不成功当前查找区间缩小一半，递归找，即可

```javascript
const basicSearch = function (mainStr, str, offset) {
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
const multiQuery = function ({
  str,
  dataArr = [],
  keyArr
}) {
  if (!Array.isArray(dataArr)) throw '数据必须是一个数组'; // 校验是数组
  if (!str || typeof str !== 'string') throw '需要搜索的必须是一个字符串'; // 需要搜索的字符串是必须

  let arr = []
  for (let i = 0; i < dataArr.length; i++) {
    let row = dataArr[i];
    if (!keyArr || keyArr && keyArr.length === 0) {
      // 如果多字段不存在,或者数量等于0; 适用于 List<String>
      let mainStr = row.toLowerCase();
      str = str.toLowerCase();
      let offset = basicSearch(mainStr, str)
      if (offset !== -1) {
        arr.push(row)
        // arr.reverse()  // 是否进行倒序
      }
    } else if (keyArr && keyArr.length !== 0) {
      // 如果多字段存在; 适用于 List<Map<K,V>>
      for (let j = 0; j < keyArr.length; j++) {
        let key = keyArr[j];
        if (typeof row[key] === 'string') {
          // 先全部转换成小写字母
          let mainStr = row[key].toLowerCase()
          str = str.toLowerCase()
          // 获取二分法返回的下标
          let offset = basicSearch(mainStr, str)
          if (offset !== -1) {
            if (arr.indexOf(row) === -1) {
              arr.push(row);
            }
            // 倒序
            arr.reverse()
          }
        }
        if (Array.isArray(row[key])) {
          // 如果是数组的话，执行递归
          let queryArr = multiQuery({
            str: str,
            dataArr: row[key],
            keyArr: keyArr
          })
          if (queryArr.length !== 0) {
            arr.push(dataArr[i])
          }
        } else if (Object.prototype.toString.call(row[key]) === '[object Object]') {
          let queryArr = multiQuery({
            str: str,
            dataArr: [row[key]],
            keyArr: keyArr
          })
          if (queryArr.length !== 0) {
            arr.push(dataArr[i])
          }
        }
      }
    } else {
      // 如果以上两种情况都不是，则返回一个未经查询的list
      arr = [...dataArr];
    }
  }
  return arr;
}
```

## 使用函数并打印
```javascript
const models = [{
    name: 'foo',
    age: 14521,
    assetInfo: [{
      sex: '男',
    }, {
      sex: '女',
    }]
  },
  {
    name: 'foo',
    age: 145,
    info: [{
      sex: '男',
    }, {
      sex: '男',
    }]
  }, {
    name: 'foo',
    age: 2,
    info: {
      sex: '男',
    }
  }, {
    name: 'foo',
    age: 2,
    info: {
      sex: '男',
    }
  }, {
    name: 'foo',
    age: 2,
    info: {
      sex: '男',
      a: {
        val: 'a',
        b: {
          val: 'b',
          c: {
            val: 'c',
          }
        }
      }
    }
  }, {
    name: 'foo',
    age: 2,
    info: {
      sex: '男',
    }
  }, {
    name: 'foo',
    age: 2,
    info: {
      sex: 'nv',
    }
  }, {
    name: 'foo',
    age: 2,
    info: {
      sex: '男',
    }
  }, {
    name: 'bar',
    age: 4
  }
];
const matchs = ['name', 'age', 'info', 'a', 'b', 'c', 'val']

console.log(multiQuery({
  str: 'a',
  dataArr: models,
  keyArr: matchs
}));
```