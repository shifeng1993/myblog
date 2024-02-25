---
title: 【js】常用的一些基础算法
categories: JS
date: 2019-08-30 23:52:43
tags:
  - JS
  - 算法
---

## 数组去重
在这里使用的方法是用我们的数据新建一个`Set`对象，然后把它转换成数组（假如需要的话）：
```javascript
// 返回去重后的数组
const arr = [1, 2, 1, 1, 2, 1, 3, 4, 1 ];
const uniq = [...new Set(arr)] // => [ 1, 2, 3, 4 ];
const uniq2 = Array.from(new Set(arr)) // => [ 1, 2, 3, 4 ];
```
uniq和uniq2两种方法都可以转换成统一的数组。

在转换成数组之前，记得`Set`对象本身也有很多有用的方法哦，比如`Size`或者`has`

## 冒泡排序
```javascript
const arr = [1, 2, 3, 4, 6, 7, 3, 3, 2, 4, 6, 33, 5, 4, 234, 323, 2, 2, 24, 5, 7, 7, 2];

const swap = (i, j, array) => {
  var temp = array[j];
  array[j] = array[i];
  array[i] = temp;
}

const bubbleSort = (array) => {
  var length = array.length, isSwap;
  for (var i = 0; i < length; i++) {            //正序
    isSwap = false;
    for (var j = 0; j < length - 1 - i; j++) {     //正序
      array[j] > array[j + 1] && (isSwap = true) && swap(j, j + 1, array);
    }
    if (!isSwap)
      break;
  }
  return array;
}

console.log(bubbleSort(arr)) // => [ 1, 2, 2, 2, 2, 2, 3, 3, 3, 4, 4, 4, 5, 5, 6, 6, 7, 7, 7, 24, 33, 234, 323 ]
```

## 根据属性来更新一个数组中的对象
```javascript
// 更新数组中对象的属性
const json = [{id: 1, score: 1}, {id: 2, score: 2}, {id: 3, score: 4}];

const newValue = {id: 3, score: 3};

const update = (newValue, alldata) => alldata.map(x => x.id === newValue.id ? newValue : x);

console.log(update(newValue, json))// => [ { id: 1, score: 1 }, { id: 2, score: 2 }, { id: 3, score: 3 } ]
```

## 根据数组内对象的某个属性进行排序
```javascript
const keysrt = (key, desc) => (a, b) => desc ? ~~(a[key] < b[key]) : ~~(a[key] > b[key]);

const arr = [{id: 1, name: "a"}, {id: 3, name: "c"}, {id: 2, name: "b"}, {id: 4, name: "d"}];

arr.sort(keysrt('name', true));
arr.sort(keysrt('name', false));
arr.sort(keysrt('id', false));
```

## 根据属性删除数组中的一个对象
```javascript
// 根据属性删除数组中的对象
const json = [{id: 1, score: 1}, {id: 2, score: 2}, {id: 3, score: 4}];
const removeId = 3;

const datafilter = (id, data) => data.filter(x => x.id !== id);

console.log(datafilter(removeId, json)) // => [ { id: 1, score: 1 }, { id: 2, score: 2 } ]
```

## 删除一个对象上的属性（key）
es6解构赋值
```javascript
const a = {foo: 1, bar: 2, useless: 3};
const {useless, ...newObj} = obj;
console.log(newObj) // => {foo: 1, bar: 2};
```

## 合并数组中的对象
下面的代码中，我们既可以合并对象，也可以根据它们的属性来更新数组项。
```javascript
// merge an array of objects
const data = [ {a: 1}, {b: 2}, {c: 3} ]
const merge = (data) => data.reduce((a, b) => ({...a, ...b}), {});
console.log(merge(data)) // => { a: 1, b: 2, c: 3  }

// merge an array of objects by property
const toMerge = [
  { id: 1, value: 'a', },
  { id: 2, value: 'b', },
  { id: 3, value: 'c' },
  { id: 1, score: 1 },
  { id: 2, score: '2' },
]
const mergedByProperty = toMerge.reduce((result, obj) => ({
  ...result,
  [obj.id]: {
    ...result[obj.id],
    ...obj
  }
}), {})
console.log(mergedByProperty) // =>
/*
 *{ '1': { id: 1, value: 'a', score: 1 },
 *  '2': { id: 2, value: 'b', score: '2' },
 *  '3': { id: 3, value: 'c' } }
 */
```

## 扁平化（Flatten）
1. 递归
```javascript
const arr = [[1, 2], [3, 4], [[5, 6, [7, 8, [9, 10]]]]];

const flatten = (arr) => {
  let newarr = [];
  arr.map(item => {
    if (Array.isArray(item)) {
      newarr = newarr.concat(flatten(item));
    } else {
      newarr.push(item);
    }
  })
  return newarr;
}

console.log(flatten(arr));// => [ 1, 2, 3, 4, 5, 6, 7, 8, 9, 10 ];
```

2. 递归+reduce简化
```javascript
const arr = [[1, 2], [3, 4], [[5, 6, [7, 8, [9, 10]]]]];
const flatten = (arr) => arr.reduce((a, b) => a.concat(Array.isArray(b) ? flatten(b) : b), []);
console.log(flatten(arr));// => [ 1, 2, 3, 4, 5, 6, 7, 8, 9, 10 ];
```

## 成对（FromPairs）
```javascript
// fromPairs
const pairs = [['a', 1], ['b', 2], ['c', 3]]
const toMap = (pairs) => pairs.reduce((res, [key, value]) => ({...res, [key]: value}), {})
console.log(toMap(pairs)) // => { a: 1, b: 2, c: 3  }
```

## 两个`Set`对象相减
```javascript
const s1 = [ 1, 2, 3, 4, 5 ]
const s2 = [ 2, 4 ]
const subtract = (s1, s2) => s1.filter(x => s2.indexOf(x) < 0);
console.log(subtract(s1,s2)) // => [ 1, 3, 5 ]
```

## 去掉字符串首尾空格
```javascript
const trim = (str) => str.replace(/(^\s*)|(\s*$)/g, '')
console.log(trim(' foo bar ')) // => 'foo bar'
```

## 升维，固定列秩
```javascript
const getMatrix = (arr, column) => {
  let newarr = [];
  if (!arr || !!arr && arr.length === 0 || typeof arr !== 'object') return newarr;
  arr.map((item, index) => {
    if (index - Math.floor(index / column) * column === 0) {
      newarr[Math.floor(index / column)] = [];
    }
    newarr[Math.floor(index / column)].push(item);
  })
  return newarr;
}
console.log(getMatrix(['foo', 'bar', 'tol', 'hi'],2)); // => [['foo', 'bar'], ['tol', 'hi']]
```

## 树结构转化为数组
```js
// 普通写法
let a = function (arr, pid, extendArr) {
  let newArr = extendArr ? extendArr : [];
  if (Array.isArray(arr)) {
    arr.forEach(({children, ...item}) => {
      newArr.push(!pid ? {...item} : {...item, pid});
      children && a(children, item.id, newArr);
    })
  }
  return newArr;
}

// 递归写法
let b = (arr, pid) => {
  return Array.isArray(arr) ? arr.reduce((prev, {children, ...cur}) => {
    return prev.concat([!pid ? {...cur} : {...cur, pid}, ...a(children, cur.id)])
  }, []) : arr
}

let tree = [
  {
    name: 'asd',
    id: 1,
    children: [
      {
        name: '11',
        id: 11,
        children: [
          {
            name: '111',
            id: 111,
            children: []
          }
        ]
      },
      {
        name: '12',
        id: 12,
        children: [
          {
            name: '121',
            id: 121,
            children: []
          }
        ]
      }, {
        name: '13',
        id: 13,
        children: [
          {
            name: '131',
            id: 131,
            children: []
          }
        ]
      }
    ]
  }
]

console.log(a(tree))
console.log(b(tree))
```

## 数组转为树结构
```js
function arrToTree(list) {
  let temp = {};
  let tree = [];
  for (let i in list) {
    temp[list[i].id] = list[i];
  }
  for (let i in temp) {
    if (temp[i].pid) {
      if (!temp[temp[i].pid].children) {
        temp[temp[i].pid].children = [];
      }
      temp[temp[i].pid].children.push(temp[i]);
    } else {
      tree.push(temp[i]);
    }
  }
  return tree;
}

let arrTree = [
  {name: 'asd', id: 1},
  {name: 'asd2', id: 2},
  {name: '11', id: 11, pid: 1},
  {name: '111', id: 111, pid: 11},
  {name: '1111', id: 1111, pid: 111},
  {name: '11111', id: 11111, pid: 1111},
  {name: '11', id: 12, pid: 2},
  {name: '111', id: 121, pid: 12},
  {name: '1111', id: 1211, pid: 121},
  {name: '11111', id: 12111, pid: 1211}
]

console.log(arrToTree(arrTree))
```

## 下划线写法和驼峰写法互转
```js
transformField(str, prefix = '', rmPrefix) {
    let newstr = '';
    if (rmPrefix) {
      const reg = new RegExp(rmPrefix);
      str = str.replace(reg, '');
      // console.log(str);
    }
    if (/\_/g.test(str)) {
      // 如果是下划线写法，则转化成驼峰 
      newstr = str.split('_').map((char, index) => (index === 0 ? char : char.slice(0, 1).toUpperCase() + char.slice(1))).join('');
    } else {
      // 如果是驼峰则转成下划线 
      newstr = str.replace(/[A-Z]/g, (...args) => (`_${args[0].toLowerCase()}`));
    }
    // console.log(prefix + newstr);
    return prefix + newstr;
  },
```

## 对象的key值转化为驼峰或者下划线
```js
function transformField(str, prefix = '', rmPrefix) {
  let newstr = '';
  if (rmPrefix) {
    const reg = new RegExp(rmPrefix);
    str = str.replace(reg, '');
    // console.log(str);
  }
  if (/\_/g.test(str)) {
    // 如果是下划线写法，则转化成驼峰 
    newstr = str.split('_').map((char, index) => (index === 0 ? char : char.slice(0, 1).toUpperCase() + char.slice(1))).join('');
  } else {
    // 如果是驼峰则转成下划线 
    newstr = str.replace(/[A-Z]/g, (...args) => (`_${args[0].toLowerCase()}`));
  }
  // console.log(prefix + newstr);
  return prefix + newstr;
}

function dateFormat(date, format) {let fmt = format || 'yyyy-MM-dd hh:mm:ss'; const o = {'M+': date.getMonth() + 1, 'd+': date.getDate(), 'h+': date.getHours(), 'm+': date.getMinutes(), 's+': date.getSeconds(), 'q+': Math.floor((date.getMonth() + 3) / 3), 'S+': date.getMilliseconds()}; if (/(y+)/.test(fmt)) {fmt = fmt.replace(RegExp.$1, (`${date.getFullYear()}`).substr(4 - RegExp.$1.length));} for (const k in o) {if (new RegExp(`(${k})`).test(fmt)) {fmt = fmt.replace(RegExp.$1, (RegExp.$1.length === 1) ? (o[k]) : ((`00${o[k]}`).substr((`${o[k]}`).length)));} } return fmt;}

const isObject = (val) => Object.prototype.toString.call(val) === '[object Object]';
const isArray = (val) => Object.prototype.toString.call(val) === '[object Array]';
const isDate = (val) => Object.prototype.toString.call(val) === '[object Date]';

// 对象的key值转化为驼峰或者下划线 deep 配置deep为false需要进行深度的递归
function transformObj(obj, options = {}) {
  let {prefix, rmPrefix, deep, format} = options;
  if (!prefix) {
    prefix = '';
  }
  if (!rmPrefix) {
    rmPrefix = '';
  }
  if (!deep) {
    deep = false;
  }
  if (!format) {
    format = 'yyyy-MM-dd hh:mm:ss';
  }
  // 筛选出 array object 
  if (isArray(obj) || isObject(obj)) {
    const handleObj = o => {
      const n = {};
      Object.keys(o).forEach(key => {
        if (!deep) {
          n[transformField(key, prefix, rmPrefix)] = isDate(o[key]) ? dateFormat(new Date(o[key]), format) : o[key];
        } else {
          n[transformField(key, prefix, rmPrefix)] = isArray(o[key]) || isObject(o[key]) ? transformObj(o[key], {deep}) : (isDate(o[key]) ? dateFormat(new Date(o[key]), format) : o[key])
        }
      });
      return n;
    };
    return Array.isArray(obj) ? obj.map(item => handleObj(item)) : handleObj(obj);
  }
  if (isDate(new Date(obj))) {
    return dateFormat(new Date(obj), format);
  }
  return obj;
}

let obj = [{
  article_id: '123',
  article_name: 'hello',
  article_create_time: new Date(),
  article_create_user: {
    user_id: '111',
    user_name: 'admin',
    user_create_time: new Date()
  },
  article_thumb_user: [
    {
      user_id: '111',
      user_name: 'admin',
      user_create_time: new Date()
    },
    {
      user_id: '111',
      user_name: 'admin',
      user_create_time: new Date()
    },
  ]
}]

console.log(transformObj(obj, {deep: true}))
```

## 删除数组或者对象中某个字段
```js
const isObject = (val) => Object.prototype.toString.call(val) === '[object Object]';
const isArray = (val) => Object.prototype.toString.call(val) === '[object Array]';

/**
 * @description: 删除数组或者对象中某个字段
 * @param {Array} fields 需要删除的字段数组
 * @param {Array || Object} obj 原始数据
 * @param {Object} options // 配置 deep 为是否递归
 * @return: 删除字段后的 array 或者 object
 */
function rmField(fields, obj, options = {}) {
  let {deep} = options; if (!deep) {deep = false;}
  // 筛选出 array object 
  if (isArray(obj) || isObject(obj)) {
    const handleObj = o => {
      const n = {}; Object.keys(o).forEach(key => {
        if (!fields.includes(key)) {
          n[key] = !deep ? o[key] : (n[key] = isArray(o[key]) || isObject(o[key]) ? rmField(fields, o[key], {deep}) : o[key]);
        }
      });
      return n;
    };
    return Array.isArray(obj) ? obj.map(item => handleObj(item)) : handleObj(obj);
  }
  return obj;
}

let obj1 = {name: 1, id: 1};
let obj2 = [{name: 1, id: 1}]

console.log(rmField(['name'], obj1, {deep: true})) // { id: 1 }
console.log(rmField(['id'], obj2, {deep: true})) // [ { name: 1 } ]
```

## 计算某个时间和当前时间差
```javascript
const getDateTimeDiff = (dateStr) => {
  let newDate = new Date();
  var ms = newDate.getTime() - new Date(dateStr.replace(/-/g, "/")).getTime();   //时间差的毫秒数      

  //计算出相差天数
  var days = Math.floor(ms / (24 * 3600 * 1000))
  //计算出小时数
  var leave1 = ms % (24 * 3600 * 1000)    //计算天数后剩余的毫秒数
  var hours = Math.floor(leave1 / (3600 * 1000))
  //计算相差分钟数
  var leave2 = leave1 % (3600 * 1000)        //计算小时数后剩余的毫秒数
  var minutes = Math.floor(leave2 / (60 * 1000))
  //计算相差秒数
  var leave3 = leave2 % (60 * 1000)      //计算分钟数后剩余的毫秒数
  var seconds = Math.round(leave3 / 1000)
  return `${days}天${hours}小时${minutes}分钟${seconds}秒`
}
```

## 格式化时间格式成固定字符串格式
```javascript
  // 格式化时间
  const dateFormat = (dateTime, format)=> {
    const date = new Date(dateTime)
    let fmt = format || 'yyyy-MM-dd hh:mm:ss'
    const o = {
      'M+': date.getMonth() + 1,
      'd+': date.getDate(),
      'h+': date.getHours(),
      'm+': date.getMinutes(),
      's+': date.getSeconds(),
      'q+': Math.floor((date.getMonth() + 3) / 3),
      S: date.getMilliseconds()
    }
    if (/(y+)/.test(fmt)) fmt = fmt.replace(RegExp.$1, (`${date.getFullYear()}`).substr(4 - RegExp.$1.length))
    for (const k in o) {if (new RegExp(`(${k})`).test(fmt)) fmt = fmt.replace(RegExp.$1, (RegExp.$1.length === 1) ? (o[k]) : ((`00${o[k]}`).substr((`${o[k]}`).length)))}
    return fmt
  }
```

## 时间戳和时间格式互相转换
```javascript
const date = new Date();
// 时间转时间戳
const timeStamp = (date) => Math.round(date.getTime() / 1000);
// 时间戳转时间
const newDate = (timeStamp) => new Date(parseInt(timeStamp) * 1000)

console.log(timeStamp(date)); // => 1537940059
console.log(newDate(timeStamp(date))); // => 2018-09-26T05:34:19.000Z
```

## 计算字符串长度和限制字符串长度
```javascript
const strLen = (str) => {
  return str.replace(/[\u0391-\uFFE5]/g, "aa").length;  //先把中文替换成两个字节的英文，在计算长度
};

const lineEllipsis = (str, len) => {
  return (strLen(str) > len) ? `${str.substr(0, len)}...` : `${str}`;
}

console.log(strLen('qwerty')); // => 8
console.log(lineEllipsis('qwerty', 6)) // => 'qwerty...'
```
