---
title: IntelliJ IDEA 使用技巧
date: 2018-05-06 21:24:29
tags: 
  - IDEA
categories:
  - 笔记
---

工欲善其事必先利其器，新公司的开发工具是IDEA，熟悉一下IDEA神器的使用。

<!-- more -->

# <font color = "#159957">IDEA界面</font>

- ``Alt + 数字键`` 切换IDEA界面窗口，重复按键隐藏窗口，ESC键返回编码区
例如通过Alt + 1 打开/关闭 Project 窗口：
![](/images/article_pictures/idea/8e466a8e-968b-4658-8a8f-ff86c62f9838.png)

- ``Ctrl + Shift + A`` 搜索Action对应快捷键：
![](/images/article_pictures/idea/6df97286-551d-442f-847e-7169ae2f8fb0.png)

# <font color = "#159957">高效定位代码</font>

## <font color = "#159957">跳转</font>

### <font color = "#159957">项目跳转</font>
- ``Ctrl + Alt + [``  和 ``Ctrl + Alt + ]`` 切换项目

### <font color = "#159957">文件跳转</font>
- ``Ctrl + E`` 最近浏览文件
- ``Ctrl + Shift + E`` 最近修改文件

### <font color = "#159957">修改位置跳转</font>
- ``Ctrl + Shift + Backspace`` 上次修改位置

### <font color = "#159957">浏览位置跳转</font>
- ``Ctrl + Alt + 左箭头`` 和 ``Ctrl + Alt + 右箭头`` 切换代码浏览位置

### <font color = "#159957">书签位置跳转</font>
- ``Ctrl + F11`` 设置标签

### <font color = "#159957">收藏位置</font>
- ``Alt + Shift + F`` 收藏方法或类(光标在方法名上时收藏方法)，``Alt + 2``查看收藏

## <font color = "#159957">搜索</font>

- ``Ctrl + Shift + F`` 全局搜索文件内容
- ``Shift + Shift`` 搜索类、文件、函数名
- ``Ctrl + N`` 搜索类，在搜索界面再按一次可搜索不在项目中的类
![](/images/article_pictures/idea/cb4113e8-29c8-4f9b-8d42-f5fcc0483db9.png)
- ``Ctrl + Shift + N`` 搜索文件
- ``Ctrl + Alt + Shift + N`` 搜索函数名

# <font color = "#159957">代码小助手</font>

## <font color = "#159957">列操作，批量编辑</font>

- ``Ctrl + Shift + Alt + J`` 批量选中
- ``Ctrl + Alt + L`` 批量格式化代码
 - ``Ctrl + W`` 选中这个单词
 - ``Ctrl + Shift + U`` 大小写转换
 例如批量修改下图枚举类：
 ![](/images/article_pictures/idea/183d3df3-d964-4264-b934-1893f94a1a58.png)
 批量修改后：
 ![](/images/article_pictures/idea/0bae90a4-bda5-4f0b-b5cc-f9caa7022101.png)

## <font color = "#159957">live templates 自定义缩写模板</font>
- ``Ctrl + Shift + A`` 找到模板设置
![](/images/article_pictures/idea/08556d29-8e9c-43d7-9128-dd43199a1972.png)
- 编写模板，使用时输入psc再回车，光标会停留在VAR1处，输入完再回车光标会停在VAR2处，再回车光标停在END处
![](/images/article_pictures/idea/98c84341-e8f4-4cd5-ae35-aa8aa6f3dfd8.png)

## <font color = "#159957">Postfix Completion</font>
常用Postfix：
- ``foo.fori`` 快速生成for循环
![](/images/article_pictures/idea/ad411891-aacc-4468-80fe-54ff9702cb41.png)
- ``foo.return`` 快速生成return语句 ``return foo;``
- ``foo.sout`` 快速生成打印语句 ``System.out.println(foo)``
- ``foo.field`` 快速生成成员变量 
![](/images/article_pictures/idea/082d4916-c33c-4f23-8506-fb42dad7525e.png)
- ``foo.nn`` 快速判断非空
![](/images/article_pictures/idea/6e7a8cde-bfdf-4df4-8c65-9b3a34e9d06e.png)

## <font color = "#159957">show intention Actions(Alter + Enter)</font>

常用操作：
- 自动创建函数
![](/images/article_pictures/idea/a9e36762-af8b-4005-8225-fee3f783bbb7.png)
- 替换for循环为foreach循环
![](/images/article_pictures/idea/b2d12b54-a5b4-4c63-830b-99efc8f17de4.png)
- 字符串fomart()或StringBuilder.append()代替字符串拼接
![](/images/article_pictures/idea/c94450c5-617c-440b-9b1f-139657e0a69d.png)
- 快速实现接口
![](/images/article_pictures/idea/03a30682-8140-45eb-98ab-ce403611f5f1.png)
- 导入依赖包
- 单词拼写
![](/images/article_pictures/idea/a0e8a3f4-c5cc-4c03-a0e1-8f4d5b212609.png)

# <font color = "#159957">编写高质量代码</font>

## <font color = "#159957">重构</font>
- ``Shift + F6`` 批量修改变量名

## <font color = "#159957">抽取</font>
![](/images/article_pictures/idea/7ca9dd57-63a3-4fde-b913-06c2dcc7e398.png)
- ``Ctrl + Alt + V`` 抽取变量
![](/images/article_pictures/idea/3bb6b469-4d8a-4862-ae12-c9c24e223aab.png)
- ``Ctrl + Alt + C`` 抽取变量为静态变量
- ``Ctrl + Alt + F`` 抽取变量为成员变量
- ``Ctrl + Alt + P`` 抽取变量为方法参数
- ``Ctrl + Alt + M`` 抽取方法

# <font color = "#159957">寻找代码修改轨迹</font>

## <font color = "#159957">在git/svn集成下</font>
- Annotate，找到代码作者
![](/images/article_pictures/idea/b2b146e4-4475-4a21-b8e9-795544439773.jpg)
- ``Ctrl + Alt + Shift + 上下箭头`` 遍历修改记录
- ``Ctrl + Alt + Z`` Revert,还原

## <font color = "#159957">本地情况 Local History</font>
- Show History(查看本地修改记录) & Put Label(相当于给自己的本地修改做一份存档)
![](/images/article_pictures/idea/1b7ca222-7314-4db6-8471-ebb5e8201df1.png)

# <font color = "#159957">关联</font>

## <font color = "#159957">关联Spring</font>
- 操作：``Project Structure``- ``Facets`` - ``Add`` - ``Spring`` - ``选择Spring配置文件``
![](/images/article_pictures/idea/8d1e5cd0-30a6-4329-94e5-aad1767781ab.jpg)
![](/images/article_pictures/idea/9e65ff98-c051-4d3f-8a5b-8deec59c6b6e.jpg)

## <font color = "#159957">关联数据库</font>
- 关联设置
![](/images/article_pictures/idea/e0618bca-8ecf-4686-ad32-490e5a3fbdf8.png)
- 关联数据库后使用时会提示关联数据库中的表名和字段：
![](/images/article_pictures/idea/02751ce7-a027-4784-8b4e-d30dc974cff9.jpg)
- 在关联数据中 ``Shift + F6``可批量重命名表名和字段：
![](/images/article_pictures/idea/9a4344c0-f329-4784-bd93-0dbb24f05fda.jpg)
- 批量修改时可以通过``Exclude``排除不需要修改的地方：
![](/images/article_pictures/idea/9b8702ac-efc8-4c4a-a534-474aa7efe703.jpg)

# <font color = "#159957">调试</font>

## <font color = "#159957">断点调试</font>
- ``Ctrl + F8`` 添加/取消 断点
- ``Shift + F9`` 开始调试
- ``F8`` Debug模式下，单步运行
- ``F9`` Debug模式下，Resume，跳到下一个断点
- ``Ctrl + Shift + F8`` 查看所有断点
- ``Mute Breakpoints`` 禁用所有断点
![](/images/article_pictures/idea/4919f699-b7f6-48fe-b954-1c50e3fc9fe8.jpg)
- ``Ctrl + Shift + F8`` 在需要用条件断点的断点处，添加条件断点
![](/images/article_pictures/idea/92ce9932-e318-4408-8841-6516dae0776e.jpg)
- ``Alt + F8`` 查看表达式值
- ``Alt + F9`` 运行到指定行（光标所在行）
- ``F2`` Debug模式下setValue

## <font color = "#159957">单元测试</font>
- ``Ctrl + Shift + F9`` 运行当前上下文，单元测试中常用
- ``Alt + Shift + F9`` 在当前可运行列表中选择一个运行

# <font color = "#159957">其他</font>

## <font color = "#159957">文件操作</font>
- ``Ctrl + Alt + Insert`` 当前目录下新建文件
- ``F5`` 复制文件
- ``F6`` 移动文件 

## <font color = "#159957">文本操作</font>
- 选中文件名，``Ctrl + C`` 复制文件名
- 选中文件名，``Ctrl + Shift + C`` 复制文件路径名

## <font color = "#159957">结构图</font>
- ``Ctrl + F12`` 查看类结构，包括方法、变量等
- ``Ctrl + Alt + U`` 或者 ``Ctrl + H`` 查看类的继承关系
- ``Ctrl + Alt + H`` 查看调用层次

# <font color = "#159957">课程地址</font>
>https://www.imooc.com/learn/924