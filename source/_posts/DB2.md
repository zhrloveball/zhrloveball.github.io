---
title: DB2常用命令整理
date: 2018-02-08 22:24:29
tags: 
  - DB2
categories:
  - 笔记
---

DB2 是美国IBM公司开发的一套关系型数据库管理系统，它主要的运行环境为UNIX（包括IBM自家的AIX）等，DB2主要应用于大型应用系统，具有较好的可伸缩性，具有与平台无关的基本功能和SQL命令，整理最近项目用到的DB2命令。

<!-- more -->

### <font color = "#159957">DB2常用操作</font>

* 连接数据库，替换{}中的内容： 
    ``db2 connect to {DBNAME} user {USERNAME} using {PASSWORD}``
* 执行脚本文件： 
    ``db2 -tvf scripts.sql ``

### <font color = "#159957">DB2常用查看命令</font>

* 查看数据库： 
    ``db2 list db directory`` 
* 查看数据库连接： 
    ``db2 list application``
* <font color = "#FE4365">查看数据库配置</font>： 
    ``db2 get db cfg ``
    * 查看日志文件路径： 
        ``db2 get db cfg | grep -i log``
* 查看SQL错误，替换{}中的内容： 
    ``db2 ? {SQLSTATE}`` 或者 ``db2 ? sql{SQLCODE}``

### <font color = "#159957">DB2离线备份</font>

1. 查看数据库连接： ``db2 list application``
2. 断开所有连接： 
    ``db2 force applications all`` 
    若还有数据库连接可强制重启数据库 
    ``db2stop force db2start ``
3. 备份，备份成功返回一个时间戳： 
    ``db2 backup database {DBNAME} to /home/bak`` 
4. 恢复： 
    ``db2 restore database {DBNAME} from /home/bak TAKEN AT 20180208225029 without ROLLING FORWARD``

### <font color = "#159957">DB2数据迁移</font>

1. 将table表中所有数据导出到test.del文件，以特殊符号``0x0f``分割，编码格式为UTF-8 ：
    ``export to test.del of del modified by coldel0x0f nochardel codepage = 1208 select * from schema.table``
2. 将test.del文件中内容导入table2表，分隔符、编码同上，GBK编码为1386： 
    ``import from test.del of del modified by coldel0x0f nochardel codepage = 1208 insert into schema.table2``