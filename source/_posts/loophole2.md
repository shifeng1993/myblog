---
title: 【查漏-js】2.数组的迭代方法 every some
categories: base
date: 2019-01-10 15:44:00
tags:
  - base
  - js
---
数组的迭代以前使用较多是 `filter`、 `forEach`、 `map` 三种方法。

- filter 是返回 返回函数为true项 组成的数组。
  ```javascript
  let list = [1, 2, 3, 4];
  list = list.filter((item) => {
    return item > 2;
  });

  console.log(list); // [3, 4]
  ```

- forEach 是循环每一项。但是没有返回值
  ```javascript
  let arr = [];
  list.filter((item, index)=>{
    arr.push(item)
  })
  ```

- map 同样是循环每一项。但是有返回值
  ```javascript
  list.filter((item)=>{
    return true
  })
  ```
# every
every比较常用的场景是一个表单校验的情况，
在各项均校验 都为true的时候，整个表单才返回true

代码:
```javascript
let list1 = [false, false, true]
let list2 = [true, true, true]

console.log(list1.every((item) => item)); // false
console.log(list2.every((item) => item)); // true
```


# some
some比较常用的场景是手风琴菜单在只能打开一个的情况下，判断打开状态，
只要有任何一个为true，就返回true;
```javascript
let list1 = [false, false, true]
let list2 = [false, false, false]

console.log(list1.some((item) => item)); // true
console.log(list2.some((item) => item)); // false
```