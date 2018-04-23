---
title: 【nodejs】linux下安装nodejs
categories: nodejs
date: 2017-01-31
tags:
  - nodejs
---
# 准备命令
centos
```bash
yum -y install gcc make gcc-c++ openssl-devel wget
```

ubuntu
```bash
apt-get install python gcc make g++
```

# 下载源码以及解压
```bash
wget https://nodejs.org/dist/v6.10.2/node-v6.10.2.tar.gz
tar -zvxf node-v6.10.2.tar.gz
```

# 配置和编译

```bash
cd node-v6.10.2
./configure
make
```

# 安装
```bash
make install
```

# 验证node安装是否成功
```bash
node -v
```