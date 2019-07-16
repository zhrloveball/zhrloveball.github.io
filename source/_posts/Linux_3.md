---
title: Linux常用命令(三)：文件打包与解压缩
date: 2018-01-28 20:15:07
tags:
  - Linux 
---

学习打包命令tar及常用解压缩命令。

<!-- more -->

### <font color = "#159957">tar</font>

#### 归档

归档就是将多个文件打包为单个文件以便于管理，默认的归档不会执行压缩。

#### 语法

* 创建归档 ``-c`` : 显示指令执行过程: ``tar -cvf /tmp/test/mylog.tar /tmp/test/*.log`` 
    * ``-f`` : 指定备份文件，需放在参数最右侧
    * ``-v`` : 显示操作过程
* 展开归档 ``-x`` : ``tar -xvf mylog.tar`` 
* 展开归档至指定文件中 ``-C`` : ``tar -xvf mylog.tar -C /tmp/newtest``
* 查看归档 ``-t`` : ``tar -tf mylog.tar``
* ``-z`` 或 ``--gzip`` 或 ``--ungzip`` ：通过gzip指令处理备份文件
    * 打包后，以 gzip 压缩 : ``tar -zcvf log.tar.gz log2012.log``
* ``-j`` ：支持bzip2解压文件。
    * 打包后，以 bzip2 压缩 : ``tar -jcvf log.tar.bz2 log2012.log``
* ``-Z`` 或 ``--compress`` 或 ``--uncompress`` ：通过compress指令处理备份文件

#### 注意

* 多个选项可以合并，但 ``-f`` 由于要带参数，因此要放到最右侧 如：``-cf`` , ``-xf``
* 选项的引导符 ``-`` 可省略。如： ``tar xvf``

### <font color = "#159957">解压缩命令</font>

* gzip/gunzip：其对应的是 .gz 结尾的压缩格式文件
* bzip2/bunzip2：其对应的是 .bz2 结尾的压缩格式文件
* xz/unxz： 其对应的是 .xz 结尾的压缩格式文件
* zip/unzip：其对应的是 .zip 结尾的压缩格式文件
* compress/uncompress：对应 .Z 结尾的压缩格式文件

#### <font color = "#159957">gzip/gunzip</font>

* 压缩:gzip
    * 常用选项
        * ``-d`` ：解压缩，相当于gunzip
        * ``-c`` ：将压缩或解压缩的结果输出至标准输出: ``gzip -c FILE > /PATH/TP/SOMEFILE.gz``
        * ``-#`` ：1-9，指定压缩比，值越大压缩比越大  如： ``gzip -9 m``
        * ``-v`` ：显示详情
    * 示例
        * 压缩，删除原文件，保留压缩后以.gz结尾的文件 ``gzip /tmp/test/messages``
        * 解压缩 ``gzip -d /tmp/test/messages.gz``
        * 压缩到至标准输出 ``gzip -c /tmp/test/messages > /tmp/test/messages.gz``
        * 解压缩到标准输出 ``gzip -d -c /tmp/test/messages.gz > /tmp/test/messages``
* 解压缩:gunzip
    * 示例
        * 解压缩，原来的压缩文件被删除，保留解压缩后的文件 ``gunzip /tmp/test/messages.gz``

#### <font color = "#159957">bzip2/bunzip2</font>

bzip2和gzip命令的使用方式基本相同，压缩或解压缩后都会删除源文件。
* bzip2
    * 常用选项
        * ``-k`` ：keep, 保留原文件 ``bzip2 -k /tmp/test/messages``
        * ``-v`` : 解压缩文件时，显示详细的信息
        * ``-#`` ：1-9，压缩比，默认为6
        * ``-d`` ：解压缩
        

bunzip2命令解压缩由bzip2指令创建的”.bz2"压缩包。
* bunzip2
    * 常用选项
        * ``-k`` : 保留原文件
        * ``-v`` : 解压缩文件时，显示详细的信息
        * ``-#`` ：1-9，压缩比，默认为6
    * 示例
        * 将/opt目录下的etc.zip、var.zip和backup.zip进行压缩，设置压缩率为最高，同时在压缩完毕后不删除原始文件，显示压缩过程的详细信息。 ``bzip2 -9vk /opt/etc.zip /opt/var.zip /opt/backup.zip``


#### <font color = "#159957">xz/unxz</font>

* xz
    * 常用选项
        * ``-k`` ：keep, 保留原文件 ``xz -k /tmp/test/messages``
        * ``-d`` ：解压缩
        * ``-#`` ：1-9，压缩比，默认为6

#### <font color = "#159957">compress/uncompress</font>

compress是个历史悠久的压缩程序，文件经它压缩后，其名称后面会多出".Z"的扩展名。当要解压缩时，可执行uncompress指令。

* compress
    * 常用选项
        * ``-r``：递归处理
        * ``-d``: 解压缩
        * ``-v``：显示指令执行过程
        * ``-c``：将结果送到标准输出，无文件被改变
    * 示例
        * 解压缩 ``compress -d man.config.Z``
        * 将 man.config 压缩成另外一个文件来备份 ``compress -c man.config > man.config.back.Z``

#### <font color = "#159957">zip/unzip</font>

zip命令可以用来解压缩文件，或者对文件进行打包操作。
* zip
    * 常用选项
        * ``-q``: 不显示指令执行过程
        * ``-r``: 递归处理，将指定目录下的所有文件和子目录一并处理
    * 示例
        * 将/home/Blinux/html/这个目录下所有文件和文件夹打包为当前目录下的html.zip： ``zip -q -r html.zip /home/Blinux/html``

unzip命令用于解压缩由zip命令压缩的“.zip”压缩包。
* unzip
    * 常用选项
        * ``-d<目录>`` ：指定文件解压缩后所要存储的目录
        * ``-n`` : 解压缩时不要覆盖原有的文件 ``unzip -n test.zip -d /tmp``
        * ``-o`` : 不必先询问用户，unzip执行后覆盖原有的文件 ``unzip -o test.zip -d tmp/``

### <font color = "#159957">参考文档</font>

http://blog.51cto.com/1992tao/1899605
http://man.linuxde.net/