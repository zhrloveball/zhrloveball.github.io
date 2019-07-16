---
title: Linux常用命令(一)：目录基本操作
date: 2018-01-25 21:48:49
tags:
  - Linux 
---

今天跟着老大去生产环境上了一下补丁，发现好多Linux命令不熟，整理一下方便查阅。

<!-- more -->

## <font color = "#159957">Terminal简单操作</font>

* 上/下方向 键：浏览历史执行命令
* Home/End 键：移动光标到本行开头/结尾
* Ctrl + c ：终止当前程序
* Tab 键：输入部分命令或文件名，按Tab可以自动补全
* \* 键 ：**模糊匹配，当要操作的文件或目录很长时，可以使用\*号匹配**
* ``man`` 查看命令详情： ``man tar``

## <font color = "#159957">目录基本操作</font>

### <font color = "#159957">权限</font>

* 访问者(UGO模型)
    * 文件和文件目录的所有者:``U``User 
    * 文件和文件目录的所有者所在的组的用户:``G``Group
    * 其它用户:``O``Others
* 事物属性

| 权限 |  缩写 | 全写 | 数字表示 | 对象是文件 | 对象是目录 |
| :--: | :--: | :--: | :--: | :--: | :--: |
| 读 | r | Read | 4 | 可以读取文件内容 | 可以浏览该目录信息| 
| 写 | w | Write | 2 |可以修改文件内容 | 可以删除移动目录内文件| 
| 执行 | x | Execute | 1 |可以执行文件 | 可以进入目录 |  

### <font color = "#159957">ls</font>

* 原意：list
* 作用：显示目录文件
* 常用：

```Bash
# 显示所有文件，包括隐藏文件
ls -a
# 输出长格式列表 ,可简写为ll
ls -l
# 用文件和目录的更改时间排序
ls -t
```

### <font color = "#159957">cd</font>

* 原意：change directory
* 作用：切换目录
* 常用：

```Bash
# 进入用户主目录
cd ~  
# 返回进入此目录之前所在的目录
cd -
# 返回上级目录
cd ..
# 切换到指定目录
cd /tmp/test/20180124
```

### <font color = "#159957">pwd</font>

* 原意：print working directory
* 作用：显示当前目录
* 常用：

```Bash
pwd
```

### <font color = "#159957">mkdir</font>

* 原意：make directories
* 作用：创建新目录
* 常用：

```Bash
# -p 递归创建目录
mkdir -p /tmp/test/20180124
# -m 创建目录的同时设置目录的权限
mkdir -p -m 755 /tmp/test/20180125
# 创建多个目录，目录之间用空格隔开
mkdir -p -m 755 /tmp/test/20180124 /tmp/test/20180125
```

### <font color = "#159957">rmdir</font>

* 原意：remove empty directories
* 作用：删除空目录
* 常用：

```Bash
# -p 删除指定目录后，若该目录的上层目录已变成空目录，则将其一并删除
rmdir -p /tmp/test/20180125/test
```

### <font color = "#159957">rm</font>

* 原意：remove
* 作用：删除一个目录/文件。<font color="red">无法恢复rm删除的文件</font>。rm命令可以用-i选项，输入y并按Enter键，才能删除文件。``指定被删除的文件列表，如果参数中含有目录，则必须加上-r或者-R选项。``
* 常用：

```Bash
#-r或-R：递归处理，将指定目录下的所有文件与子目录一并处理
#-i：删除已有文件或目录之前先询问用户
#-f：强制删除文件或目录
rm -ri 20180124
```

### <font color = "#159957">cp</font>

* 原意：copy
* 作用：复制文件或目录
* 常用：

```Bash
#-r 复制目录,递归复制子目录及文件
#-p 保留源文件或目录的属性
#-i 覆盖既有文件之前先询问用户

#将20180124文件夹内容复制到20180125中，并保持目录属性
[Bran@localhost test]$ cp -rpi 20180124 ./20180125
cp：是否覆盖"./20180125/20180124/test.txt"？ y
```

### <font color = "#159957">mv</font>

* 原意：move
* 作用：剪切文件/重命名
* 常用：

```Bash
# -b：当文件存在时，覆盖前，为其创建一个备份
# -i：交互式操作，覆盖前先行询问用户

# 将b文件夹中所有文件移动到文件夹a中，若有文件重复则备份并提示
[Bran@localhost test]$ mv -bi b/* a
mv：是否覆盖"a/test.txt"？ y
[Bran@localhost test]$ cd b
[Bran@localhost a]$ ll
-rw-rw-r-- 1 Bran Bran 0 1月  25 23:58 test.txt
-rw-rw-r-- 1 Bran Bran 0 1月  25 23:58 test.txt~
```

## 参考

http://man.linuxde.net/