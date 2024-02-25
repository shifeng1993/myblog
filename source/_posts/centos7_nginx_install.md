---
title: 【linux】centos7下nginx安装和Nginx二级域名的配置
categories: linux
date: 2017-09-28
tags:
  - linux
  - nginx
---
# nginx安装
## 安装
依次运行下面命令
```bash
# 将nginx放到yum repro库中
rpm -ivh http://nginx.org/packages/centos/7/noarch/RPMS/nginx-release-centos-7-0.el7.ngx.noarch.rpm
# 查看nginx信息
yum info nginx
# 使用yum安装ngnix
yum install nginx
# 启动nginx
service nginx start
# 设置开机启动
systemctl enable nginx
# 查看nginx版本
nginx -v
```
之后就可以访问公网ip进行测试了
成功则会出现`Welcome to nginx!`

## 扩展知识
```bash
# 修改nginx配置文件
vim /etc/nginx/conf.d/default.conf
# 重载服务
/usr/sbin/nginx -s reload
# 启动一个服务
systemctl start firewalld.service
关闭一个服务
systemctl stop firewalld.service
# 重启一个服务
systemctl restart firewalld.service
# 显示一个服务的状态
systemctl status firewalld.service
# 在开机时启用一个服务
systemctl enable firewalld.service
# 在开机时禁用一个服务
systemctl disable firewalld.service
# 查看服务是否开机启动
systemctl is-enabled firewalld.service;echo $?
# 查看已启动的服务列表
systemctl list-unit-files|grep enabled
```

## 可能遇到的问题
```bash 
具体情况如下  1。本机能ping通虚拟机  2。虚拟机也能ping通本机  3。虚拟机能访问自己的web  4。本机无法访问虚拟己的web  这个问题的原因是服务器的80端口没有打开或防火墙没有关闭
```
解决方案：
```bash 
systemctl status firewalld.service
systemctl stop firewalld.service
systemctl disable firewalld.service
systemctl status firewalld.service
```

# 阿里云域名解析，Nginx二级域名的配置
## 需求
我的nodeAPI项目跑在3000端口上，但是购买的阿里云ECS有nginx服务支持80端口。我可以这样访问我的项目，api.shifeng1993.com:3000，但是哪里有见过域名后面加端口的，因此，将3000端口映射到80上。

## 配置云解析
在阿里云解析将二级域名解析到 ECS 的IP地址，
注意不要写端口号。添加a记录

## 端口映射
### 在项目目录下新建nginx配置文件
开启node项目
```bash
# 启动
pm2 start app.js
# 开机启动设置
pm2 startup
# 保存当前运行的项目到开机启动
pm2 save
```

新建 `yourNodeProject/nginx.conf`
```bash
server {
  listen 80;
  server_name api.shifeng1993.com;
  location / {
    root /home/mongo-server;
    index static/index.html
    proxy_set_header   X-Real-IP $remote_addr;
    proxy_set_header   Host      $http_host;
    proxy_pass         http://127.0.0.1:3000;
  }
}
```

将项目配置软链到nginx配置下
```bash
ln -s yourNodeProject/nginx.conf /etc/nginx/conf.d/lab.beacelee.com.conf
```

重启nginx
```bash
systemctl restart nginx.service
```

## 测试
输入你的api域名，就可以看到对应的服务器转发端口了。