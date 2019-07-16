---
title: Linux常用命令(五)：Java开发常用命令
date: 2018-01-30 20:20:56
tags:
 - Linux
---

日常开发中常用的Linux命令总结。

<!-- more -->

* 查看当前用户: ``id``
* 切换用户: ``su -用户名``
* 查看环境变量: ``env``
* 查看当前ip: ``ifconfig -a``
* 查看当前进程: ``ps -ef``
* 查看当前Java进程: ``ps -ef | grep java``
* 强制终止进程: ``kill -9 进程号``
* 查看文件系统使用情况: ``df -kg``
* 查看当前目录及子目录使用情况: ``du -kg``
* 查看日志文件: ``tail -f test.log``
* 统计文本行数: ``wc -l test.txt`` 
* 查看文件属性: ``ls -l`` 或 ``ll``
* 修改文件属性: ``chmod 755 test.txt``
* 解压a.doc.tar.gz
    * 第一种方法:
        1. ``gzip -dc /usr/test/a.doc.tar.gz``
        2. ``tar -xvf /usr/test/a.doc.tar``
    * 第二种方法，使用管道：``gizp -dc /usr/test/a.doc.tar.gz | tar -xvf``  
    * 使用tar参数 ``-z`` : ``tar -xzvf /usr/test/a.doc.tar``
