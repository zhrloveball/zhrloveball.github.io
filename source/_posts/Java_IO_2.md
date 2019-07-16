---
title: Java流与文件学习笔记(二)：节点流
date: 2018-01-14 20:28:51
tags: 
  - Java IO
categories:
  - 笔记
---

学习整理Java流之节点流。Java IO框架很好地体现了对象的多态性，通过不同的子类实例化，实现的功能也不同。

<!-- more -->

## <font color = "#159957">节点流</font>

### <font color = "#159957">文件流</font>

* Java文件流分为FileInputStream / FileOutputSream 和 FileReader / FileWriter
* FileInputStream / FileOutputSream 是 InputStream / OutputStream的直接子类，但是 FileReader / FileWriter 却是转换流 InputStreamReader / OutputSteamWriter的子类

#### <font color = "#159957">FilOutputStream </font>

* 三个写数据方法：

```Java
public void write(int b) throws IOException;//写一个字节
public void write(byte b[]) throws IOException;//写一个字节数组
public void write(byte b[], int off, int len) throws IOException;//写一个字节数组的一部分
```
```Java
try(
        //JDK1.7 在try() 括号中打开流，最后程序会自动关闭流对象
        //public FileOutputStream(String name, boolean append),这个构造方法第二个参数为true时表示在文件中写数据时是追加数据而不是覆盖数据
        FileOutputStream fos = new FileOutputStream("F:" + File.separator + "demo.txt" , true);
) {
    byte[] bytes = {97, 98, 99, 100, 101};
    fos.write(97);
    fos.write("\r\n".getBytes());
    fos.write(bytes, 1, 3);
    //Window系统的换行符:\r\n,Linux系统的换行符:\n,Mac系统的换行符:\r
    //demo.txt文件内容是两行,第一行是a,第二行是bcd,因为ASCII中97,98,99,100表示a,b,c,d
}
```

#### <font color = "#159957">FileInputStream</font>

* 对应的，也有三个读数据方法：

```Java
public int read() throws IOException;//返回的是实际读取的字节个数
public int read(byte b[]) throws IOException;
public int read(byte b[], int off, int len);
```
```Java
try(
    FileInputStream fis = new FileInputStream("F:" + File.separator + "demo.txt" )
    //文件内容：abcde
) {
    //第一次读取
    int by = fis.read();
    System.out.println(by);//97
    System.out.println((char) by);//a

    //第二次读取
    by = fis.read();
    System.out.println(by);//98
    System.out.println((char) by);//b

    //第三次读取
    by = fis.read();
    System.out.println(by);//99
    System.out.println((char) by);//c

    //代码重复，用循环代替
    //当read方法读取到文件末尾而没有更多的数据，则返回 -1
    int len;
    while ((len = fis.read()) != -1) {
        System.out.println((char) len);//d e
        //强制转化为单个字节输出时，遇到中文会出现乱码
    }
}
```

#### <font color = "#159957">复制文件</font>

```Java
try (
        //封装数据源
        FileInputStream fis = new FileInputStream("F:" + File.separator + "demo.txt");
        //封装目的地
        FileOutputStream fos = new FileOutputStream("F:" + File.separator + "demo2.txt");
        //读取的文件不存在会报错，写入的文件不存在会自动创建
) {

//    int len;
//    while ((len = fis.read()) != -1) {
//        fos.write(len);
//    }

    //一次读一个字节数组，效率更高
    byte[] bytes = new byte[1024];//一次读取1k的数据
    int len;
    while ((len = fis.read()) != -1) {
        fos.write(bytes, 0, len);
    }
}
```

#### <font color = "#159957">FileReader 和 FileWriter</font>

 操作与 FileInputStream 和 FilOutputStream 类似，操作对象从字节变成了字符。
```Java
try (
        FileReader fr = new FileReader("F:" + File.separator + "demo.txt");
        FileWriter fw = new FileWriter("F:" + File.separator + "demo1.txt")
) {
    char[] chars = new char[1024];
    int len;
    while ((len = fr.read(chars)) != -1) {
        fw.write(chars, 0, len);
        fw.flush();
    }
}
```

### <font color = "#159957">管道流</font>

* Java管道流分为PipedInputStream / PipedOutputStream 和 PipedReader / PipedWriter
* 管道流用来在多个线程之间进行信息传递，若用在同一个线程中可能会造成死锁
* 一对管道流包含一个缓冲区，其默认值为1024个字节，若要改变缓冲区大小，可以使用带有参数的构造函数
* 管道的读写操作是互相阻塞的，当缓冲区为空时，读操作阻塞；当缓冲区满时，写操作阻塞
* 管道依附于线程，因此若线程结束，则虽然管道流对象还在，仍然会报错“read dead end”
* 基本流程：
  * 创建管道输出流PipedOutputStream 和 管道输入流 PipedInputStream
  * 建立连接
  * PipedOutputStream 向管道中写入数据，PipedInputStream 从管道中读取数据

```Java
public class Sender extends Thread {

    private PipedOutputStream out =null;
    public PipedOutputStream getOutputStream()
    {
        this.out=new PipedOutputStream();
        return out;
    }

    public void run() {
        String s = new String("Receiver,你好！");
        try {
            out.write(s.getBytes());
            out.close();
        } catch (IOException e) {
            System.out.println( e.getMessage() );
        }

    }
}
```
```Java
public class Receiver extends Thread {

    private PipedInputStream in = null;
    public PipedInputStream getinputStream()
    {
        this.in = new PipedInputStream();
        return in;
    }

    public void run ( )
    {
        String s = null;
        byte [] buf = new byte[1024];
        try {
            int len = in.read( buf );
            s= new String( buf,0,len);
            System.out.println("收到了一下消息："+s);
            in.close();
        } catch (IOException e) {
            System.out.println( e.getMessage() );
        }
    }
}
```
```Java
public class PipeDemo {

    public static void main(String[] args) throws IOException{

        Sender sender = new Sender();
        Receiver receiver = new Receiver();
        PipedOutputStream out = sender.getOutputStream();
        PipedInputStream in = receiver.getinputStream();
        out.connect(in);
        sender.start();
        receiver.start();
		//收到了一下消息：Receiver,你好！
    }
}
```

### <font color = "#159957">内存操作流</font>

#### <font color = "#159957">字节数组流 & 字符数组流</font>

* 字节数组流 ByteArrayInputStream 和 ByteArrayOutputStream
* 字符数组流 CharArrayReader 和 CharArrayWriter
* 使用时在内存中开辟了一个字节/字符数组的缓冲区
* 示例：

```Java
//流中的字符转换大写小
public static void main(String[] args) throws IOException{

    String str = "HELLO WORLD";
    try(
    ByteArrayInputStream input = new ByteArrayInputStream(str.getBytes());
    ByteArrayOutputStream output = new ByteArrayOutputStream();
    ) {
        int len;
        while ((len = input.read()) != -1) {
            char ch = (char) len;
            output.write(Character.toLowerCase(ch));
        }
        System.out.println(output.toString());
    }

}
```

#### <font color = "#159957">字符串流</font>

* StringReader 和 StringWriter
* 当须使用一个Reader或者Writer来作为参数传递参数，且数据源又仅仅是一个String类型数据，无需从文件中写出，就可以用字符串流。
* StringWriter 中内置了一个StringBuffer成员变量，不会创建多个字符串而造成资源浪费。
* 示例：

```Java
public static void main(String[] args) throws IOException{

    try (
        StringReader sr = new StringReader("Hello World");
        StringWriter sw = new StringWriter()
    ) {
        int c;
        while((c = sr.read()) != -1){
            sw.write(c);
        }
        //这里展示了即使关闭了StringWriter流，但仍然能获取到数据，因为其close方法是一个空实现。
        sw.close();
        System.out.println(sw.getBuffer().toString());
    }
}
```







