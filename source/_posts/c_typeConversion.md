---
title: 【C学习随笔】C语言的类型转换
categories: C/C++
date: 2017-03-20
tags:
  - C
---
## 自动类型转换
涉及两种类型运算，两种类型会分别被转换成两种类型的更高级别，类型的从高到低依次为：
long double
double
float
unsigned long long
long long 
unsigned long
long
unsigned int
int
例外的情况是，当long和int的大小相同时，unsigned int的级别会比long的级别高。之所以没有列出short和char，是因为他们已经自动被升级到int或者unsigned int。
类型升级通常不会有什么问题，类型降级会真的麻烦，原因是：一个char储存整数101没问题，但是存不下22222 长度不够
浮点数转换为整数原来的浮点值会被截断，

```c
#include <stdio.h>
int main(void){
	int a = 1.6+1.7;
	printf("%d\n", a);
	return 0;
}
```
## 强制类型转换
通常应该避免自动类型转换。利用强制类型转换可以更好的控制运算。但是两种类型转换，本质上都是一样的。

示例：
```c
#include <stdio.h>
int main(void){
	int a = (int)1.6+(int)1.7;
	printf("%d\n", a);
	return 0;
}
```

