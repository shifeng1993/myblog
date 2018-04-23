---
title: 【C学习随笔】gdb的编译选项和命令
categories: C/C++
date: 2017-03-20
tags:
  - C
---
# gdb的编译选项和命令
注：以下文件名称`binFile`表示编译后的文件，`srcFile`表示编译前的源文件

`gcc  -g -o [binFile] [srcFile]`

`gdb  [binFile]`

## gdb调试流程

`l(list)`：查看所载入的文件

`b(break)`：设置断点，程序运行到断点即可停止

`info b`：查看设置的断点情况

`r(run)`：从第一行开始运行代码，或者从制定行开始，可以在r后面加上行号

`p n`：查看变量n的值

`n(next)`：单步运行下一行代码（遇到函数不会进入函数）

`s(step)`：单步运行下一行代码（遇到函数会进入函数）

`c(continue)`：恢复程序的运行，执行剩余的程序