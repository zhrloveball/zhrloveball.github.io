---
title: Linux常用命令(二)：文件基本操作
date: 2018-01-27 00:29:15
tags:
  - Linux 
---

常用的创建文件、查找文件、查看文件、编辑文件命令。

<!-- more -->

## <font color = "#159957">文件基本操作</font>

### <font color = "#159957">文件处理</font>

#### <font color = "#159957">touch</font>

touch命令用来创建空文件。例如``touch test.txt``

### <font color = "#159957">文件查找</font>

#### <font color = "#159957">find</font>

**find**命令用来在指定目录下查找文件。

##### 语法

``find [搜索范围] [匹配条件]``

##### 常用
* 在/home目录下查找以.txt结尾的文件名： ``find /home -name "*.txt"``
  * 忽略大小写： ``find /home -iname "*.txt"``
  * 查找所有以.txt结尾**或者**.pdf结尾的文件：`` find . -name "*.txt" -o -name "*.pdf"``
  * 查找所有以.txt结尾**并且**.pdf结尾的文件：`` find . -name "*.txt" -a -name "*.pdf"``
  * 查找所有以.txt结尾**且不以**.pdf结尾的文件：`` find . -name "*.txt" -not -name "*.pdf"``
* 根据文件类型搜索：**f**代表普通文件，**l**代表符号连接，**d**代表目录
  * 查找当前目录下文件小于1M并且文件类型是一般文件的文件：``find ./ -size -1M -a -type f``  

#### <font color = "#159957">locate</font>

**locate**命令其实是``find -name``的另一种写法，但是要比后者快得多，原因它直接搜索一个**含有本地所有文件信息**的数据库``/var/lib/locatedb``，该数据库每天自动更新一次，在使用locate前可使用``suod updatedb``手动更新数据库。

* 查找某个文件：``locate jdk-7u80-linux-x64.rpm``

#### <font color = "#159957">whereis</font>

whereis命令用来定位指令的二进制程序、源代码文件和man手册页等相关文件的路径

* 查出说明文档路径：``whereis -m svn``
* 找source源文件: ``whereis -s svn``
  
#### <font color = "#159957">which</font>

which命令可以看到某个系统命令是否存在，以及执行的到底是哪一个位置的命令。例如``which pwd``

### <font color = "#159957">文件内容查看</font>

#### <font color = "#159957">more</font>

more用来分页显示文件内容。空格键翻页，回车键换行，q键退出。例如``more /etc/services``

#### <font color = "#159957">less</font>

less也是分页显示文件内容，但是**可以向上翻页**，用PageUp键向上翻页，用PageDown键向下翻页。

#### <font color = "#159957">head</font>

head用来显示文件开头的内容，默认显示前面10行。可以用-n指定显示行数，例如``head -n 20 /etc/services``

#### <font color = "#159957">tail</font>

tail用来显示文件尾部内容，也是默认显示末尾10行。常用来观察动态日志文件:``tail -f test.log``

#### <font color = "#159957">cat</font>

cat经常用来显示文件的内容,但是文件过大时，文本会在屏幕上迅速闪过，往往看不清所显示的内容。因此，一般用more等命令分屏显示。

### <font color = "#159957">文件编辑</font>

#### <font color = "#159957">vi</font>

vi命令是常用的文本编辑器。

* i：在当前字符前插入文本
* Esc：从编辑模式切换到命令模式
* :wq：在命令模式下，执行存盘退出操作
* :w：在命令模式下，执行存盘操作
* :w！：在命令模式下，执行强制存盘操作
* :q：在命令模式下，执行退出vi操作
* :q！：在命令模式下，执行强制退出vi操作

## 参考

http://man.linuxde.net/


