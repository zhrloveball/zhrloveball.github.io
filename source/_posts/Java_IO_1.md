---
title: Java流与文件学习笔记(一)：Java流的介绍与分类
date: 2018-01-11 15:31:51
tags: 
  - Java IO
categories:
  - 笔记
---



复习整理Java IO 知识，温故而知新。



<!-- more -->



## <font color = "#159957">Java流</font>

### <font color = "#159957">分类</font>

* 根据流的方向分为：输入流、输出流
  * 从其中读入一个字节序列的对象叫**输入流**，向其中写入一个字节序列的对象叫**输出流**
  * 字节序列的来源和目的地可以是：文件、内存、网络连接等
* 根据数据单位分为：字节流、字符流 
  * 为了处理以**Unicode**（两个字节）形式存储的信息 ，出现了字符流
* 根据流的角色分为：节点流、处理流
  * 节点流：直接与数据源相连，读入或读出；
  * 处理流：在节点流的基础上对之进行加工，进行一些功能的扩展。处理流的构造器必须要传入节点流的子类

![](/images/article_pictures/IOCategories.jpg)

* Java IO是采用的是装饰模式，即采用处理流来包装节点流的方式，来达到代码通用性。比如：
```Java
  FileInputStream  fis = new FileInputStream(srcPath);
  BufferedInputStream  bis = new BufferedInputStream(fis);
```
  BufferedInputStream装饰了原有的InputStream，让普通的InputStream也具备了缓存功能，提高了效率。

* 节点流在新建时需要一个数据源（文件、网络）作为参数，而处理流需要一个节点流作为参数。节点流都是对应抽象基类的实现类，它们都实现了抽象基类的基础读写方法。

### <font color = "#159957">输入流对应关系</font>

![](/images/article_pictures/IO1.jpg)

### <font color = "#159957">输出流对应关系</font>

![](/images/article_pictures/IO2.jpg)

### <font color = "#159957">读写字节</font>

* InputStream和OutputStream是基本的输入字节流和输出字节流，分别定义了读/写的抽象方法

```Java
public abstract int read() throws IOException;
```
```Java
public abstract void write(int c) throws IOException;
```

* read方法从字节流中读取一个字节，若到了末尾则返回-1，否则返回读入的字节。
* read和write方法执行时将**阻塞**，直至字节被读入或写出。即如果流不能被立即访问，当前线程将阻塞，其他线程将执行有用的工作。
  * 可以使用available()方法判断当前可读入字节数量防止阻塞：

```Java
InputStream in = new FileInputStream("F:" + File.separator + "demo.txt");
int bytesAvailable = in.available();
if (bytesAvailable > 0) {
	byte[] data = new byte[bytesAvailable];
    in.read(data);
}
in.close();
```

* 完成对流的读写后，应调用close方法释放有限的操作系统资源。
  * 关闭一个输出流的同时会冲刷该输出流的缓冲区，可以调用flush方法人为冲刷这些输出

### <font color = "#159957">读写字符</font>

  对于Unicode文本，可以使用抽象类Reader和Writer的子类，Reader和Writer类的基本方法与InputStream和OutputStream中的方法类似

```Java
public int read() throws IOException
```
```Java
public void write(int c) throws IOException
```

### <font color = "#159957">Closeable,Flushable,Readable,Appendable</font>

* InputStream实现了Closeable接口
* OutputStream实现了Flushable，Closeable接口
* Reader实现了**Readable**, Closeable接口
* Writer实现了**Appendable**, Flushable , Closeable接口
* Closeable、Flushable接口很简单，分别只拥有以下方法

```Java
public void close() throws IOException;
```
```Java
void flush() throws IOException;
```

* Java 1.7开始，Closeable继承AutoCloseable接口，在 try() 括号中打开流，最后程序会自动关闭流对象，不再需要显示地close。
* Readable接口只有一个方法：
```Java
//CharBuffer是字符缓冲区，拥有按顺序和随机进行读写的方法。
public int read(java.nio.CharBuffer cb) throws IOException;
```
* Appendable接口有用于添加单个字符和字符序列的方法：
```Java
Appendable append(char c) throws IOException;
//CharSequence接口描述了一个char值序列的基本属性,包括length()、charAt(int index)、toString()等。String,StringBuffer, StringBuilder,CharBuffer都实现了它
Appendable append(CharSequence csq) throws IOException;
```

