---
title: Linux常用命令(四)：文件传输命令ftp
date: 2018-01-29 21:15:26
tags:
  - Linux
---

使用ftp命令在本地机和远程机之间传送文件。因为经常会到测试环境拿文件，将ftp命令单独拿出来学习。
转载自[如何在命令行中使用 ftp 命令上传和下载文件](https://linux.cn/article-6746-1.html)。

<!-- more -->

### <font color = "#159957">文件传输命令FTP</font>

#### <font color = "#159957">建立FTP连接</font>

```Bash
ftp 192.168.0.1
```

#### <font color = "#159957">使用用户名密码登录</font>

```Bash
Name: root
Password:
```
登陆成功后终端命令行开头会变成``ftp>``

#### <font color = "#159957">使用 FTP 下载文件</font>

1. 使用lcd命令设定本地接受目录位置
```Bash
lcd /home/user/yourdirectoryname
```
**如果你不指定下载目录，文件将会下载到你登录 FTP 时候的工作目录。**

2. 使用**get**命令下载文件
```Bash
get test.txt
```

#### <font color = "#159957">使用 FTP 上传文件</font>

使用``put /path/test.txt``上传文件，可以用通配符同时上传多个文件:``put /path/*.txt``

#### <font color = "#159957">关闭 FTP 连接</font>

有三个命令可以关闭FTP连接：
* bye
* exit
* quit


 
