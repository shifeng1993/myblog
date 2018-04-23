---
title: 【C学习随笔】C语言的字符串
categories: C/C++
date: 2017-03-20
tags:
  - C
---
## 定义
字符串是一个或多个字符的序列，例如：
```c
"wo xian zai hao e wo hao xiang chi fan "
```
双引号不是字符串的一部分，仅告知编译器，它们中间的字符数组为字符串。


## 字符串存储
因为C语言没有单独用来存储字符串的变量类型。所以字符串都被存储在char类型的数组里面，数组由连续的存储单元组成，字符串中的字符被储存在相邻的存储单元中，每个单元储存一个字符。
例如：
```c
w o   x i a n   z a i   h a o   e   w o   h a o   x i a n g   c h i   f a n \0
```
结尾用一个空字符\0来表示字符串的结束。

`%s`来告诉printf()打印了一个字符串
```c
#include <stdio.h>
int main(void){
	char name[] = "wo shi haise";
	printf("%s\n", name );
	return 0;
}
```
