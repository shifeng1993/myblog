---
title: 【C学习随笔】macos系统下安装gdb
categories: C/C++
date: 2017-03-20
tags:
  - C
---

# 1.安装homebrew
`ruby -e"$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"`

# 2.安装gdb
`brew install gdb`

# 3.生成证书
生成证书

Step One:

自带工具：**钥匙串访问**
钥匙串访问 - 证书处理 - 创建证书：  
                        * 名称：gdb-cert
                        * 身份类型：自签名根证书
                        * 证书类型：代码签名

- 继续 - 继续 - ... - 直到选择证书位置 - Done.
  * 有效期：999       * 系统

--------------------------------------------------

Step Two:

左侧选择系统 - 找到 gdb-cert - 右键 - 显示简介 - 信任 - 代码签名
                        * 始终信任

--------------------------------------------------

Step Three:

自带工具：**活动监视器**
搜索 *taskgated* - 双击 - 退出 
别关闭**活动监视器**!!!!!!
ps：原因是在Mac系统（10.9）里默认不允许GNU gdb访问其他进程。

--------------------------------------------------

Step Four:

终端输入： `sudo codesign -s gdb-cert /usr/local/bin/gdb`

现在可以使用gdb了