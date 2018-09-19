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