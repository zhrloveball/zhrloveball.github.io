---
title: 记一次收获颇多的Code Review
date: 2018-08-13 16:41:31
categories:
    - 项目总结
tags:
    - Code Review
    - Mysql
    - HashMap
---

现在的团队一直有Code Review的好习惯，通过Code Review可以学习他人优秀的代码，了解项目中其他模块的业务知识，更有可能发现自己代码中隐藏的BUG。

<!--more-->


### <font color = "#159957">mysql虚拟表dual</font>

先是发现了同事写的一段SQL, 作用是在table表中插入记录时确保uuid是唯一的：

```mysql
insert into table(id, no, date, uuid)
select (id, no, date, uuid) from dual
where not exists (select * from table where uuid = ' ');
```

搜索了一下发现``dual``是虚拟表，用来构成select的语法规则，这样插入数据时判断某一字段是否已存在可以通过一条SQL就解决了。


### <font color = "#159957">HashMap使用自定义类作为Key</font>

然后一位老同事指出来我将自定义类作为HashMap的Key有风险：

HashMap、HashTable都是Key-Value存储键值对的集合，都有一个共同的特点：不能存储重复的键。
而put时判断键是否重复的步骤有两步：
1. 调用key的``hashCode()``方法生成hash值，在通过``indexFor(hash, table.length)``获取数组下标
2. 若当前数组索引位置已有值，再调用key的``equals()``方法判断是否key相同，若相同则覆盖掉原来的value，若不同，则是产生了hash冲突，会形成一个单链表，将hash值相同、key不同的元素以Entry<K,V>的方式存放在链表中，这样就解决了hash冲突。

若两个自定义对象内容相同，但因为<font color = "#FE4365">内存地址</font>不同，Object自带的equals()方法会认为这两个对象是不同对象，最终导致Map中有两个看起来一样的key。

所以用自定义类作为Key时<font color = "#FE4365">一定要重写hashCode()和equals()</font>， 可以使用lombok自带的``@EqualsAndHashCode`` 默认重写。

非常感激优秀的同事在Code Review时能指出我代码中的一些隐患和不足，还有因为我性格比较急，出现问题立马去修改，没有静下来思考一下类似的功能有没有类似的缺陷，导致代码修改很频繁，这在项目后期是一定要避免出现的，我要认真反思一下。