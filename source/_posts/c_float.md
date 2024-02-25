---
title: 【C学习随笔】C语言的实型数据
categories: C/C++
date: 2017-03-20
tags:
  - C
---
注：浮点运算是有误差的，用于精密计算建议用整型数据类型存储和运算。如：银行存款。
## float
- 单精度类型
占用4个字节，32位。
用`%f`进行输出

```c
#include <stdio.h>

int main(void) {
    float a = 3.1;
    printf("%f", a);
    return 0;
}

```

## double
- 单精度类型
占用8个字节，64位。

用`%f`进行输出
```c
#include <stdio.h>

int main(void) {
    double a = 3.1;
    printf("%f", a);
    return 0;
}

```

## long double
- 长双精度
占16个字节，128位。
用`%Lf`进行输出

```c
#include <stdio.h>

int main(void) {
    long double a = 3.1;
    printf("%Lf", a);
    return 0;
}

```