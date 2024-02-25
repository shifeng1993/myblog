---
title: 【C学习随笔】C语言其他运算符
categories: C/C++
date: 2017-03-20
tags:
  - C
---
## sizeof运算符和size_t类型
sizeof运算符，以字节为单位返回运算对象的大小，运算对象可以是一个具体的数据对象或类型，默认返回一个size_t类型的值，是一个无符号整数类型，转换说明为`%zd`(C99)，如果不支持`%zd`(C99)，可使用`%lu`或`%u`代替。
以下是一个例子：
```c
#include <stdio.h>
int main(void){
	int a = 2;
	size_t b = sizeof(a);
	printf("%d\n%zd\n", a , b);
	return 0;
}
```

## 求模运算符 %
求模运算符用于证书运算，求模运算符给出其左侧整数除以右侧的证书的余数，例如`13 % 5`得3。求模运算符只试用于整数，不能用于浮点数。
以下是例子：
```c
#include <stdio.h>
int main(void){
	int a = 13;
	int b = 2;
	int c = a % b;
	printf("%d\n", c);
	return 0;
}

```

## 递增运算符++ 与 递减运算符 - -
这两种运算符都分为两种模式，
1.前缀模式。
示例代码：
```c
#include <stdio.h>
int main(void){
	int a = 1;
	int b = ++a;
	printf("%d\n%d\n", a,b);
	return 0;
}
```
输出为2 2

1.后缀模式。
示例代码：
```c
#include <stdio.h>
int main(void){
	int a = 1;
	int b = a++;
	printf("%d\n%d\n", a,b);
	return 0;
}
```
输出为2 1

不同点：
前缀模式：b是a递增之后的值，
后缀模式：b是a递增之前的值，


