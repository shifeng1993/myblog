---
title: 【CPP学习随笔】cpp输入输出流以及作用域运算符
categories: C/C++
date: 2017-04-28
tags:
  - CPP
---
## 输入输出流
`>>`输入运算符
`<<`输出运算符
示例：
```cpp
#include <iostream>
int main () {
    int a, b;
    std::cin >> a >> b;
    std::cout << a << b <<std::endl;
    return 0;
}
```
## 作用域运算符

`::`作用域运算符

示例：
```cpp
 std::cin
```
以上就是在std命名空间下的cin。