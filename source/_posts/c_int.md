---
title: 【C学习随笔】C语言的整型数据
categories: C/C++
date: 2017-03-20
tags:
  - C
---

# 有符号整型数据
1Byte = 8Bit；一个字节为八个比特位元。
## short
或者为`short int` 占用的储存空间可能比int类型少，常用于较小数值的场合节省空间
```c
#include <stdio.h>

int main(void) {
    short int a = 10;
    printf("%d\n", a);
    return 0;
}
```


## int
只要是整数类型，都会被定义，用`%d`输出

- 十进制

```c
#include <stdio.h>

int main(void) {
    int a = 10;
    printf("%d\n", a);
    return 0;
}
```
- 二进制

用`0b`为开头进行定义
```c
#include <stdio.h>

int main(void) {
    int a = 0b100;
    printf("%d\n", a);
    return 0;
}
```
- 八进制

用0为开头进行定义
```c
#include <stdio.h>

int main(void) {
    int a = 010;
    printf("%d\n", a);
    return 0;
}
```
- 十六进制

用`0x`为开头进行定义
```c
#include <stdio.h>

int main(void) {
    int a = 0xB;
    printf("%d\n", a);
    return 0;
}
```

## long 

```c
#include <stdio.h>

int main(void) {
    long a = 10;
    printf("%d\n", a);
    return 0;
}
```

long长度受限于操作系统环境，如：当前环境32位，为4个字节，当前长度64位则为8个字节。为解决问题，使用`long long`,用`%ld`输出

```c
#include <stdio.h>

int main(void) {
    long long a = 10;
    printf("%ld\n", a);
    return 0;
}
```
此外在任何有符号类型前面添加关键字signed，可强调使用有符号类型。
# 无符号整型数据
非负数
```c
#include <stdio.h>

int main(void) {
    unsigned int a = 10;
    printf("%d\n", a);
    return 0;
}
```
比如：
八位二进制，正常范围为`-128 ~127`，加上`unsigned`后，会变成`0~255`。


## C11新规范
`stdint.h `来规范整形数据位数，不分操作平台。
包括`__int8_t`， `__int16_t`， `__int32_t`， `__int64_t`,
无负号整型数据包括`__uint8_t `， `__uint16_t `， `__uint32_t `， `__uint64_t `,
全部由`%d`来输出。

