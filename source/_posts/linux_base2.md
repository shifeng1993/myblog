---
title: 【linux系统基础学习随笔】2.linux常用命令
date: 2019-07-08 21:29:00
tags:
  - base
  - linux
  - devops
---
前言：可能总结的不是很好，后续会继续完善一些东西

-------
# 常见目录
- / 根目录
- /boot 启动目录，启动相关文件
- /dev 设备文件
- /etc 配置文件
- /home 普通用户的家目录,可以操作
- /lib 系统库保存目录
- /mnt 移动设备挂载目录
- /media 光盘挂载目录
- /misc 磁带机挂载目录
- /root 超级用户的家目录,可以操作
- /tmp 临时目录,可以操作
- /proc 正在运行的内核信息映射, 主要输出进程信息、内存资源信息和磁盘分区信息等等
- /sys 硬件设备的驱动程序信息
- /var 变量
- /bin 普通的基本命令，如ls,chmod等,一般的用户也都可以使用
- /sbin 基本的系统命令，如shutdown，reboot，用于启动系统，修复系统,只有管理员才可以运行
- /usr/bin 是你在后期安装的一些软件的运行脚本
- /usr/sbin 置一些用户安装的系统管理的必备程序

# 命令基本格式
## 命令提示符
```bash
[root@shifeng ~]#
```
- `root` 当前登录用户
- `shifeng` 主机名
- `~` 当前工作目录,默认是当前用户的home目录，root就是`/root`,普通用户是 `/home/用户名`
- `#` 提示符 超级用户是 `#`,普通用户是`$`

## 命令格式
```bash
命令 [选项] [参数]
```
- 当有多个选项时，可以写在一起
- 一般参数有简化和完整写法两种 -a 与 --all等效

## ls
- 查询目录中的内容
- 默认当前目录下的文件列表

```bash
ls [选项] [文件或者目录]
```
选项
- -a 显示所有文件，包括隐藏文件
- -l 显示详细信息
- -d 查看目录本身的属性而非子文件 ls /etc/
- -h 人性化的方式显示文件大小
- -i 显示inode,也就是i节点，每个节点都有ID号

注：ls -l  或者 ll 

|drwxr-xr-x	|.|	1|	root	|root	|800	|Sep 16 00:19	|logs|
|--	|--|--|	--	|--	|--	|--|--|
|文件类型和权限	|ACL权限|	硬链接引用计数	|所有者	|所属组	|文件大小|最后修改时间	|文件名|

# 文件处理命令
## mkdir 创建目录
```bash
mkdir -p [目录名]
```
- -p 递归创建

## cd 切换目录
```bash
cd [目录]
```
- `~` 当前用户home 目录
- `/` 根目录
- `.` 当前目录
- `..` 上级目录
- 相对路径是参照当前所在目录
- 绝对路径是从根目录开始
- 按TAB键可以补全命令和目录

## pwd 显示当前目录
```bash
pwd
```

## rmdir 删除目录
```bash
rmdir [目录名]
```

## rm 删除文件或者目录
```bash
rm [文件或者目录]
```
- `-r` 删除目录
- `-f` 强制删除
- `rm -rf [文件或者目录]` 递归强制删除所有目录

## cp 复制
```bash
copy [源文件或者目录] [目标文件]
```
- -r 复制目录,默认是复制文件
- -p 连带文件属性复制
- -d 若源文件是链接文件，则复制连接属性
- -a 相当于 -rpd

## mv 移动文件或者改名
```bash
mv [源文件或者目录] [目标文件]
```

## ln 链接
生成链接文件 link 通常使用软连接
### 软链接特征
```bash
ln -s [源文件] [目标文件]
```
- -s 创建软链接
- 类似Windows快捷方式
- 软链接拥有自己的i节点和Block块，但是数据块中只保存源文件的文件名和i节点号，并没有实际的文件数据
- lrwxrwxrwx l 软链接 软链接的文件权限都是 777
- 修改任意一个文件，另一个都会改变
- 删除源文件，软链接不能使用
- 软链接源文件必须写绝对路径

### 硬链接特征
- 拥有相同的i节点和存储block块，可以看作是同一个文件
- 可以通过i节点访问
- 不能跨分区
- 不能针对目录使用
- 一般不使用

# 文件搜索命令
## locate 在数据库中按文件名搜索
```
locate [-d ][文件名]
```
- -d或--database= 配置locate指令使用的数据库。locate指令预设的数据库位于/var/lib/slocate目录里，文档名为slocate.db。
- 速度比较快
- 数据保存在/var/lib/mlocate后台数据库，每天更新一次
- 可以updatedb命令立刻更新  数据库
- 只能搜索文件名
- /etc/updatedb.conf 建立索引的配置文件
  - PRUNE_BIND_MOUNTS = "yes" 全部生效，开启搜索限制
  - PRUNEFS 不搜索的文件系统
  - PRUNENAMES 忽略的文件类型
  - PRUNEPATHS 忽略的路径 /tmp

## whereis 搜索命令所在路径以及帮助文档所在位置
```
whereis [命令名]
```
- -b 　只查找二进制文件。
- -m 　只查找说明文件。

## which 在PATH的目录内查找文件
```
which [命令名]
```
- 能看到的都是外部安装的命令
- 无法查看Shell自带的命令，如 which cd

## 环境变量 $PATH
`PATH = /usr/local/bin:/usr/bin:/bin:/usr/sbin:/sbin`
- 定义的是系统搜索命令的路径
- echo $PATH 打印环境变量

## find 文件搜索
```
find [搜索范围] [搜索条件]
```
### 按名称搜索
避免大范围的搜索，会非常消耗系统资源
```
find / -name aaa.log
```

### 通配符
find是在系统当中搜索符合条件的文件名，如果需要匹配，使用通配符匹配，通配符是完全匹配
通配符
- `*` 匹配任意内容
- `?` 匹配任意一个字符
- `[]` 匹配任意一个中括号内的字符

```
find . -name "ab[cdef]"
```

### -i 不区分大小写
```
find / -iname A.log
```

### -user 按所有者进行搜索
```
find /root -user root
find /root -nouser
```

### 按时间搜索
```
find /nginx/access.log -mtime +5
```
|参数|	含义|
|--|--|
|atime|	文件访问时间|
|ctime|	改变文件属性|
|mtime|	修改文件内容|

|参数	|含义|
|--|--|
|-5	|5天内修改的文件|
|5	|5天前当前修改的文件|
|+5|	5天前修改的文件|

### 按大小搜索
k小写,M大写
```
find . -size 100k
```

|参数	|含义|
|--|--|
|-8k	|小于8K|
|8k	|等于8K|
|+8k|	大于8K|
|+8M	|小于8M|

### 按i节点搜索
```
find . -inum 123456
```
### 综合应用
```
find /tmp -size +10k -a -size -20k
```
查找`/tmp`目录下，大于10KB并且小于20KB的文件
- -a and 逻辑与，两个条件都满足
- -o or 逻辑或，两个条件满足一个就可以

```
find /tmp -size +10k -a -size -20k -exec ls -lh {} \;
```
- exec 对上个命令的结果进行操作

### grep 在文件当中匹配符合条件的字符串
```
grep "10" access.log
```
- -i 忽略大小写
- -v 排除指定字符串
- find命令，在系统当中搜索符合条件的文件名，如果需要匹配，使用通配符匹配，通配符是完全匹配
- grep命令 在文件当中搜索符合条件的字符串，如果需要匹配，使用正则表达式进行匹配，正则表达式时包含匹配

# 帮助命令
## 基本用法
```
man [命令]
```
获取指定命令的帮助
`man ls` 查看ls的帮助

## man的级别
1. 查看命令的帮助
2. 查看可被内核调用的函数的帮助
3. 查看函数和函数库的帮助
4. 查看特殊文件的帮助
5. 查看配置文件的帮助
##查看游戏的帮助
##查看其它的帮助
8. 查看系统管理员可用命令的帮助
9. 查看和内核相关文件的帮助

## 查看命令级别
- 1p: POSIX utilities
- POSIX表示可移植操作系统接口（Portable Operating System Interface of UNIX，缩写为 POSIX ），POSIX标准定义了操作系统应该为应用程序提供的接口标准.

```
man -f ls
whatis ls
man 1 ls
man 1p ls
```

## 关键字搜索
```
man -k passwd
```

## shell 内部帮助
- `whereis`命令找到就是外部，找不到就是内部
```
help cd
```

# 压缩与解压缩命令
.zip .gz .bz2 .tar.gz .tar.bz2

## zip格式
压缩文件或目录,是一种压缩格式

- 压缩文件 `zip 压缩文件名.zip 源文件`
- 压缩目录 `zip -r 压缩目录名.zip 源目录`
- 解压 `unzip 压缩目录名.zip`

```
mkdir book
touch book/1.txt
touch book/2.txt
zip -r book.zip book
unzip book.zip
```

## gzip
gzip为高压，可以把文件压缩得更小

|命令|示例|含义|
|--|--|--|
|gzip 源文件|gzip a.txt|压缩为.gz格式的压缩文件，源文件会消失|
|gzip -c 源文件 > 压缩文件|gzip -c yum.txt > yum.txt.gz|压缩为.gz格式的压缩文件，源文件不会消失|
|gzip -r 目录|gzip -r xx|把目录下的每个子文件都变成压缩包，并删除原文件，当前目录无变化|
|gzip -d 压缩文件名|gzip -d yum.txt.gz|解压缩文件,不保留压缩包|
|gunzip 压缩文件|gunzip yum.txt.gz|解压缩文件,也不保留压缩包|
- 压缩是压缩目录下的文件

## .bz2格式压缩
bzip2是一个压缩能力更强的压缩程序

|命令|示例|含义|
|--|--|--|
|bzip2 源文件|bzip2 1.txt|压缩为.bz2格式的文件，不保留源文件|
|bzip2 -k 源文件|bzip2 -k 1.txt|压缩为.bz2格式的文件，保留源文件|
|bzip2 -d 压缩文件名|bzip2 -d 1.txt.bz2|解压压缩包,不保留压缩包|
|bunzip2 压缩文件名|bunzip2 1.txt.bz2|解压压缩包,也不保留压缩包|
- bzip2 不能压缩目录

## tar
打包命令,只打包并不压缩
```
tar -cvf [打包文件名] [源文件]
```
- -c 打包
- -v 显示过程
- -f 指定打包后的文件名

```
tar -cvf book.tar book
```
- x 解打包

```
tar -xvf book.tar
```

## tar.gz压缩格式
- zip可以压缩目录但压缩效率不高，gzip和bzip2压缩效率高但不支持目录
- 可以先打包为`.tar`格式，再压缩为`.gz`格式 
- `-z` 压缩为`.tar.gz`格式 `-x` 解压缩`.tar.gz`格式

|命令|示例|含义|
|--|--|--|
|tar -zcvf 压缩包名 .tar.gz源文件|tar -zcvf book.gar.gz book|可以先打包为.tar格式，再压缩为.gz格式|
|tar -zxvf 压缩包名.tar.gz|tar -zxvf book.tar.gz|解压tar.gz压缩包|
|tar -jcvf 压缩包名 .tar.bz2源文件|tar -jcvf book.tar.bz2 book|可以先打包为.tar格式，再压缩为.bz2格式|
|tar -jxvf 压缩包名.tar.bz2|tar -jxvf book.tar.bz2|解压tar.bz2压缩包|

# 关机和重启命令
## shutdown 关机
- -c 取消前一个关机命令
- -h 关机
- -r 重启

```
shutdown -r 06:00
shutdown -c
```

## init
关机
```
init 0
```
重启
```
init 6
```

## logout 退出登录
```
logout
```

# 查看登录用户信息
## w
查看登录用户信息

- USER 登录的用户名
- TTY 登录的终端 tty1 本地终端 pts/0远程终端
- FROM 登录的IP
- LOGIN 登录时间
- IDLE 用户闲置时间
- JCPU 该终端所有进程占用的时间
- PCPU 当前进程所占用的时间
- WHAT 正在执行的命令

## who
查看登录用户信息

- USER 登录的用户名
- TTY 登录的终端 tty1 本地终端 pts/0远程终端
- LOGIN 登录时间（登录的IP）

## last
查看当前登录和过去登录的用户信息 默认读取 `/var/log/wtmp` 文件

- 用户名
- 登录终端
- 登录IP
- 登录时间
- 退出时间(在线时间)

## last log
查看所有用户的最后一次登录时间

- 用户名
- 登录终端
- 登录IP
- 最后一次登录时间


# 磁盘管理
## df
查看磁盘分区使用状况

|参数|描述|
|--|--|
|-l|仅显示本地磁盘(默认)|
|-a|显示所有文件系统的使用情况|
|-h|以1024进制计算最合适的单位显示磁盘容量|
|-H|以1000进制计算最合适的单位显示磁盘容量|
|-T|显示磁盘分区类型|
|-t|显示指定类型文件系统的磁盘分区|
|-x|不显示指定类型文件系统的磁盘分区|

## du
统计以磁盘上的文件大小

|参数|描述|
|--|--|
|-b|以byte为单位统计文件|
|-k|以KB为单位统计文件|
|-m|以MB为单位统计文件|
|-h|以1024为单位统计文件|
|-H|以1000为单位统计文件|
|-s|指定统计目标|

```
du -s /etc
du -sH  /etc
```

## 添加新硬盘后的分区和格式化
- 硬件设备是由linux系统自动识别并以文件的形式存在于根目录下的dev目录下
- 1-4分区编号是留给主分区和扩展分区的,逻辑分区只能从5开始
|命令|含义|中文|
|--|--|--|
|m|print this menu|打印菜单|
|n|add a new partition|添加一个分区|
|d|delete a partition|删除一个分区|
|p|print the partition table|打印分区表|
|q|quit without saving changes|退出不保存|
|w|write table to disk and exit|写入分区表并保存|

```
fdisk -l
Disk /dev/sda: 21.5 GB, 21474836480 bytes
Device      Boot      Start         End      Blocks   Id  System
/dev/sda1   *           1            26      204800   83  Linux

Disk /dev/sdb: 8589 MB, 8589934592 bytes
fdisk /dev/sdb 开始对这块硬盘进行分区
m 打印命令
n 创建一个分区
Partition number (1-4): 1 选择分区编号
First cylinder (1-1044, default 1): 1 输入开始扇区
Last cylinder, +cylinders or +size{K,M,G} (1-1044, default 1044): +3000M 输入结束扇区
p 查看当前分区
n 创建分区
e 扩展分区
l 创建逻辑分区 
d 删除分区
w 分区表写入磁盘
```

## GPT
- MBR下主分区最多4个，GPT可达128个
- MBR下主分区容量最大2TB，GPT模式下容量可达18EB(1EB=1024PB,1PB=1024TB,1TB=1024GB)
```
parted 开始分区,默认是对第一块硬盘分区
mklabel gpt  指定分区表的类型为gpt
print 查看分区表的类型
mkpart 开始分区
分区名称？  []? system                                                    
文件系统类型？  [ext2]?                                                   
起始点？ 0                                                                
结束点？ 2000                                                             
警告: The resulting partition is not properly aligned for best performance.
忽略/Ignore/放弃/Cancel? c 
(parted) 1  
结束点？ 2000  
mkpart 2th 2000 3000
quit 退出编辑
```

## 格式化
```
ls -l /dev/sdb*
mkfs.ext3 /dev/sdb1
mkfs -t ext4 /dev/sdb2
```
## 挂载
```
mkdir /mnt/zhufeng
mount /dev/sdb1 /mnt/zhufeng
umount /mnt/zhufeng
vim + /etc/fstab

/dev/sdb1  /mnt/zhufeng  ext3    defaults        0 0
分区名称      挂载点      文件系统类型  
```

## 添加swap交换分区
- 建立普通的linux分区
- 修改分区类型的16进制编码
- 格式化成交换分类
- 启动交换分区
```
fdisk /dev/sdb
p 查看当前的分区
Command (m for help): t 修改分区的系统ID
Partition number (1-4): 3 修改分区编号为3的分区
Hex code (type L to list codes): L 列出所有编号
Hex code (type L to list codes): 82 把编号修改为16进制的82
Changed system type of partition 3 to 82 (Linux swap / Solaris)
p
/dev/sdb3 767 1044 2233035 82 Linux swap / Solaris

free 查看剩余内存
mkswap /dev/sdb3  把sdb3设置为交换分区
swapon /dev/sdb3  挂载sdb3成交换区
free
swapoff /dev/sdb3
```

## 挂载
### 挂载命令格式
- mount [-t 文件系统] [-o 特殊选项] 设备文件名 挂载点
- 选项
  - -t 文件系统 ext4 iso9660
  - -o 特殊选项

### 挂载光驱
```
mkdir /mnt/cd
mount -t iso9660 /dev/sr0 /mnt/cdrom
```

### 卸载光驱
```
umount /mnt/cdrom
```

### 挂载U盘
```
fdisk -l 查看硬盘及分区信息
mount -t vfat /dev/sdb1 /mnt/usb
```
- linux默认不支持NTFS格式


# 文件查看命令
## cat
- cat 命令用于连接文件并打印到标准输出设备上。
- cat [-AbeEnstTuv] [--help] [--version] fileName
- 参数
  - -n 或 --number：由 1 开始对所有输出的行数编号。

```
cat -n textfile1
```

## more
- Linux more 命令类似 cat ，不过会以一页一页的形式显示，更方便使用者逐页阅读，而最基本的指令就是按空白键（space）就往下一页显示，按 b 键就会往回（back）一页显示，而且还有搜寻字串的功能（与 vi 相似），使用中的说明文件，请按 h 。
```
more  testfile
```

## head
- 用来显示开头某个数量的文字区块
```
head -5 readme.txt
```

## tail
- tail命令可用于查看文件的内容
- 有一个常用的参数 -f 常用于查阅正在改变的日志文件。
```
tail [参数] [文件]
```
- 参数
  - -f 循环读取
  - -n<行数> 显示文件的尾部 n 行内容

```
tail -5 mail.txt
tail -f access.log
```

## 第二页
```
head -10 file | tail -5
```