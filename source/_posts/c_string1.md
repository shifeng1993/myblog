---
title: 【C学习随笔】C语言字符长度和数据类型大小
categories: C/C++
date: 2017-03-20
tags:
  - C
---
## 字符类型大小
使用strlen()函数，可以给出字符串中的字节长度

```c
#include <stdio.h>
#include <string.h>

int main(void){
	char name[] = "wo shi shifeng";
	printf("%zd\n", strlen(name));
	return 0;
}
```

## 数据类型大小
使用sizeof()函数，可以给出数据类型中的占的字节长度

```c
#include <stdio.h>
#include <string.h>

int main(void){
	int a = 120;
	printf("%zd\n", sizeof(a));
	return 0;
}
```