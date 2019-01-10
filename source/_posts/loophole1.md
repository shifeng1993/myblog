---
title: 【查漏-js】1.js中的label语句
categories: base
date: 2019-01-10 10:02:00
tags:
  - base
  - js
---
最近再看一遍js高级程序设计，来查漏，发现涉及label语句的时候有点记不得了，其实我以前就是跳着看了一遍，这个地方肯定是漏掉了。

-----------------------
# 原文：
书上是这么写的：

label语句：
使用 label 语句可以在代码中添加标签，以便将来使用。以下是 label 语句的语法: label: statement
下面是一个示例:
    start: for (var i=0; i < count; i++) {
        alert(i);
}
这个例子中定义的 start 标签可以在将来由 break 或 continue 语句引用。加标签的语句一般都
要与 for 语句等循环语句配合使用。

-------------------------
# 理解和运用
这个例子单另理解有点困难，于是我在网上搜了一下使用label语句的场景。
一般出现在多维数组循环的跳出中。来优化性能用

## 代码
```javascript
var num = 0;
restart:
for (var i=0; i < 10; i++) {
    for (var j=0; j < 10; j++) {
        if (i == 5 && j == 5) {
            break restart;
        }
    num++;
    }
}
alert(num); //55
```
如果不使用break，会继续循环掉剩余的次数。
