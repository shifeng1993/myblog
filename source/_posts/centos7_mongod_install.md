---
title: 【linux】centos7下mongodb安装
categories: linux
date: 2017-09-19
tags:
  - linux
  - mongodb
---
# 简介
MongoDB  是一个介于关系数据库和非关系数据库之间的产品，是非关系数据库当中功能最丰富，最像关系数据库的。他支持的数据结构非常松散，是类似json的bson格式，因此可以存储比较复杂的数据类型。Mongo最大的特点是他支持的查询语言非常强大，其语法有点类似于面向对象的查询语言，几乎可以实现类似关系数据库单表查询的绝大部分功能，而且还支持对数据建立索引。

## Packages包说明
MongoDB官方源中包含以下几个依赖包：
- mongodb-org: MongoDB元数据包，安装时自动安装下面四个组件包：
- mongodb-org-server: 包含MongoDB守护进程和相关的配置和初始化脚本。
- mongodb-org-mongos: 包含mongos的守护进程。
- mongodb-org-shell: 包含mongo shell。
- mongodb-org-tools: 包含MongoDB的工具：
  - mongoimport
  - bsondump
  - mongodump
  - mongoexport
  - mongofiles
  - mongooplog
  - mongoperf
  - mongorestore
  - mongostat
  - mongotop

# 安装MongoDB3.4社区版
## 配置MongoDB的yum源
`vim /etc/yum.repos.d/mongodb-org-3.4.repo`
添加如下内容：
```bash
[mongodb-org-3.4]  
name=MongoDB Repository  
baseurl=https://repo.mongodb.org/yum/redhat/$releasever/mongodb-org/3.4/x86_64/  
gpgcheck=1  
enabled=1  
gpgkey=https://www.mongodb.org/static/pgp/server-3.4.asc
```

## 安装MongoDB
```bash
yum -y install mongodb-org
```

## 配置SELinux
```bash
setenforce 0 #不需要重启系统 
```

## 启动MongoDB
```bash
systemctl start mongod.service
systemctl status mongod.service
```

## 设置mongodb远程访问
### 添加用户
切换到admin数据库
```bash
use admin
```

进入mongo shell
```bash
mongo
```

创建root用户
```bash
db.createUser({user:'root',pwd:'123',roles:[{role:'root',db:'admin'}]})
```

创建管理员用户
```bash
db.createUser({user:'admin',pwd:'123',roles:[{role:'userAdminAnyDatabase',db:'admin'}]})
```
注:如果要修改或者删除用户请自行百度。

### 配置远程访问，开启鉴权
编辑mongod.conf
```bash
vim /etc/mongod.conf
```

注释bindIp
```bash
# bindIp: 127.0.0.1
```

去掉#security:前的注释并添加一行，如下：
```bash
security:
  authorization: "enabled"
```

重启mongodb
```bash
systemctl restart mongod.service
```

## 登录
mongo shell 服务器登录
```bash
mongo yourIP:27017 -u admin -p 123 --authenticationDatabase admin 
```

mongo shell 远程登录
```bash
mongo -u admin -p 123 --authenticationDatabase admin 
```

mongoose url
```bash
mongodb://admin:123@yourIP:27017/admin?authSource=admin
```

注意:如果登录失败，显示超时。可能是防火墙的问题，阿里云在安全策略进行配置，别的请自行百度。



# 卸载mongodb
## 停止mongodb
```bash
systemctl stop mongod.service

```
## 卸载mongodb
```bash
# 删除安装包
yum erase $(rpm -qa | grep mongodb-org) 
# 删除数据目录
rm -r /var/log/mongodb
rm -r /var/lib/mongo
```