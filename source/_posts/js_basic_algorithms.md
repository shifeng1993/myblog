---
title: 【js】常用的一些基础算法
categories: JS
date: 2018-09-19
tags:
  - JS
  - 算法
---

# 数组去重
在这里使用的方法是用我们的数据新建一个`Set`对象，然后把它转换成数组（假如需要的话）：
```javascript
// 返回去重后的数组
const numbers = [1, 2, 1, 1, 2, 1, 3, 4, 1 ];
const uniq = [...new Set(numbers)] // => [ 1, 2, 3, 4 ];
const uniq2 = Array.from(new Set(numbers)) // => [ 1, 2, 3, 4 ];
```
uniq和uniq2两种方法都可以转换成统一的数组。

在转换成数组之前，记得`Set`对象本身也有很多有用的方法哦，比如`Size`或者`has`

# 冒泡排序
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
console.log(bubbleSort(arr))
```

# 根据属性来更新一个数组中的对象
接下来我们要更新数组中`id:3`的对象
```javascript
// 更新数组中对象的属性
const initial = [ {id: 1, score: 1}, {id: 2, score: 2}, {id: 3, score: 4}];
const newValue = {id: 3, score: 3};
const updated = initial.map(x => x.id === newValue.id ? newValue : x);
console.log(updated) // => [ { id: 1, score: 1 }, { id: 2, score: 2 }, { id: 3, score: 3 } ]
```
当然，“初始”常量是保持不变的。

# 根据数组内对象的某个属性进行排序
```javascript
const keysrt = (key,desc) => (a,b)=> desc ? ~~(a[key] < b[key]) : ~~(a[key] > b[key]);

const arr = [{id:1,name:"a"},{id:3,name:"c"},{id:2,name:"b"},{id:4,name:"d"}];
arr.sort(keysrt('name',true));
arr.sort(keysrt('name',false));
arr.sort(keysrt('id',false));
```

# 根据属性删除数组中的一个对象
让我们根据`id === 3`来删除数组中的那个对象。
```javascript
// 根据属性删除数组中的对象
const removeId = 3;
const without3 = initial.filter(x => x.id !== removeId);
console.log(without3) // => [ { id: 1, score: 1 }, { id: 2, score: 2 } ]
```
当然，原始值还是保持不变。

# 删除一个对象上的属性（key）
你可以另辟蹊径：
```javascript
const a = {foo: 1, bar: 2, useless: 3};
const {useless, ...newObj} = obj;
console.log(newObj) // => {foo: 1, bar: 2};
```

# 合并数组中的对象
下面的代码中，我们既可以合并对象，也可以根据它们的属性来更新数组项。
```javascript
// merge an array of objects
const data = [ {a: 1}, {b: 2}, {c: 3} ]
const merged = data.reduce((res, obj) => ({...res, ...obj}), {})
console.log(merged) // => { a: 1, b: 2, c: 3  }

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

# 扁平化（Flatten）
```javascript
// 将数组降维
const arrayOfArray = [ [1, 2], [3, 4], [[5, 6]] ];
const flattened = arrayOfArray.reduce((res, a) => [...res, ...a], [] );
console.log(flattened) // => [1, 2, 3, 4, [5, 6]];
```

# 成对（FromPairs）
```javascript
// fromPairs
const pairs = [['a', 1], ['b', 2], ['c', 3]]
const asObjects = pairs.reduce((res, [key, value]) => ({ ...res, [key]: value }), {})

// Or event smarter (thanks to @nomaed for pointing this one out)
const asObjects2 = { ...(new Map(pairs)) }

console.log(asObjects) // => { a: 1, b: 2, c: 3  }
```

# 两个`Set`对象相减
```javascript
// subtract two sets
const s1 = [ 1, 2, 3, 4, 5 ]
const s2 = [ 2, 4 ]
const subtracted = s1.filter(x => s2.indexOf(x) < 0)
console.log(subtracted)
```

# 去掉字符串首尾空格
```javascript
const trim = (str) => str.replace(/(^\s*)|(\s*$)/g, '')
console.log(trim(' foo bar ')) // => 'foo bar'
```

# 升维，固定列秩
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

# 计算某个时间和当前时间差
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

# 格式化时间格式成固定字符串格式
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

# 时间戳和时间格式互相转换
```javascript
// 计算时间戳
const date = new Date();
// 时间转时间戳
const timeStamp = (date) =>  Math.round(date.getTime() / 1000);
// 时间戳转时间
const newDate = (timeStamp) => new Date(parseInt(timeStamp) * 1000)

```

# 计算字符串长度和限制字符串长度
```javascript
const strLen = (str) => {
  return str.replace(/[\u0391-\uFFE5]/g, "aa").length;  //先把中文替换成两个字节的英文，在计算长度
};

const lineEllipsis = (str, len) => {
  return (strLen(str) > len) ? `${str.substr(0, len)}...` : `${str}`;
}

console.log(strLen('qwertyui')); // => 8
console.log(lineEllipsis('qwertyui', 6)) // => 'qwerty...'
```
