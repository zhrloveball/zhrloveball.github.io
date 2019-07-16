---
title: Java流与文件学习笔记(三)：处理流
date: 2018-01-17 21:46:51
tags: 
  - Java IO 
categories:
  - 笔记
---

学习整理Java流之处理流。处理流在节点流的基础上对之进行加工，进行一些功能的扩展。处理流的构造器必须要传入节点流的子类。

<!-- more -->

## <font color = "#159957">处理流</font>

### <font color = "#159957">转换流</font>

* 转换流提供了在字节流和字符流之间的转换，包括InputStreamReader和OutputStreamWriter

#### <font color = "#159957">InputStreamReader</font>
  * 用于将字节流中读取到的字节按指定字符集解码成字符。需要和InputStream"套接"。
  * 构造方法：

```Java
public InputStreamReader(InputStream in)
public InputSreamReader(InputStream in,String charsetName)
//例如
InputStreamReader isr=new InputStreamReader(new FileInputStream("a.txt"),"utf-8");
```

#### <font color = "#159957">OutputStreamWriter</font>
  * 用于将要写入到字节流中的字符按指定字符集编码成字节。需要和OutputStream"套接"。
  * 构造方法

```Java
public OutputStreamWriter(OutputStream out)
public OutputStreamWriter(OutputStream out,String charsetName)
```

* 字符编码
  * GBK/GB2312：中文的国标编码，专门用来表示汉字，是双字节编码，GBK包括中文繁体，字库更大
  * Unicode：国际标准码，融合了多种文字。所有文字都用两个字节来表示,Java语言使用的就是unicode
  * UTF-8：是一种针对Unicode的可变长度字符编码，用1到6个字节编码Unicode字符。

#### <font color = "#159957">读取文件</font>

```Java
try (
    OutputStreamWriter osw = new OutputStreamWriter(new FileOutputStream("F:" + File.separator + "demo.txt"),"UTF-8");
) {
    osw.write("红鲤鱼与绿鲤鱼与驴");
}

try (
    InputStreamReader isr = new InputStreamReader(new FileInputStream("F:" + File.separator + "demo.txt"),"UTF-8");//指定为GBK时会乱码，GBK是双字节编码
) {
    int ch;
    while ((ch = isr.read()) != -1) {
        System.out.print((char)ch);//红鲤鱼与绿鲤鱼与驴
    }
}
```

### <font color = "#159957">缓冲流</font>

* 包括BufferedInputStream / BufferedOutputStream 和 BufferedReader / BufferedWriter
* BufferedlnputStream 类和BufferedOutputStream 类可以通过减少磁盘读写次数来提髙输人和输出的速度
* 使用BufferedOutputStream, 个别的数据首先写人到内存中的缓冲区中。当缓冲区已满时，缓冲区中的所有数据一次性写入到磁盘中，
* 示例

```Java
public static void bufferedStream(FileInputStream fis,FileOutputStream fos) throws IOException{

	try(
		BufferedInputStream bis = new BufferedInputStream(fis);
		BufferedOutputStream bof = new BufferedOutputStream(fos);
	){
		byte[] bytes = new byte[1024];
		int len;
		while ((len = bis.read(bytes)) != -1) {
			bof.write(bytes,0,len);
		}
	}
}
```

### <font color = "#159957">对象流</font>

* ObjectInputStream和OjbectOutputSteam
* 用于存储和读取**对象**的处理流。它的强大之处就是可以把Java中的对象写入到数据源中，也能把对象从数据源中还原回来。
* 只有支持 java.io.Serializable 或 java.io.Externalizable 接口的对象才能被ObjectInputStream/ObjectOutputStream操作
* ObjectOutputStream和ObjectInputStream**不能序列化static和transient修饰的成员变量**
* 凡是实现Serializable接口的类都有一个表示序列化版本标识符的静态变量：

```Java
//通过此常量来验证版本的一致性
private static final long serialVersionUID;
```

* 示例：

```Java
//Person类
public class Person implements Serializable{

    private static final long serialVersionUID = 1L;
    
    private String name;
    private int age;
    private float height;

    public Person(String name, int age ,float height) {
        this.name = name;
        this.age = age;
        this.height = height;
    }

	//get()set()方法和toString()
}
```

```Java
public static void main(String[] args) throws IOException,ClassNotFoundException {

	List<Person> personList = new ArrayList<>();
    personList.add(new Person("Tom", 24, 168.0f));
    personList.add(new Person("Jerry",25, 178.0f));
	//创建一个对象写入通道
	try(
		ObjectOutputStream oos = new ObjectOutputStream(new FileOutputStream("F:" + File.separator + "person.txt"));
	){
		oos.writeObject(personList);
	}
	//创建一个对象读取通达
	try (
		ObjectInputStream ois = new ObjectInputStream(new FileInputStream("F:" + File.separator + "person.txt"));
	) {
		List<Person> resultList = (List<Person>) ois.readObject();
		for (Person person : resultList) {
			System.out.println(person.toString());
			//测试结果：Person{age=24, name='Tom'} Person{age=25, name='Jerry'}
		}
	}
}
```

### <font color = "#159957">数据流</font>

* DataInputStream 和 DataOutputStream
* DataInputStream 和 DataOutputStream是FilterInputStream和FilterOutputStream的子类，分别实现了DataInput和DataOutput接口，属于包装流（处理流）,**可以直接从流中读写基本数据类型的数据**。
* 示例：

```Java
//Person类还是对象流中用的那个类，这次是读取Person类中基本数据类型的数据
public static void main(String[] args) throws IOException,ClassNotFoundException {

	List<Person> personList = new ArrayList<>();
    personList.add(new Person("Tom", 24, 168.0f));
    personList.add(new Person("Jerry",25, 178.0f));

	//创建一个对象写入通道
    try(
    	ObjectOutputStream oos = new ObjectOutputStream(new FileOutputStream("F:"+ File.separator+"person.txt"));
    ){
    	oos.writeObject(personList);
    }
    //创建一个对象读取通达
    try (
    	ObjectInputStream ois = new ObjectInputStream(new FileInputStream("F:" + File.separator + "person.txt"));
   	) {
    	List<Person> resultList = (List<Person>) ois.readObject();
        for (Person person : resultList) {
        	System.out.println(person.toString());
            //Person{age=24, name='Tom', height=168.0}
            //Person{age=25, name='Jerry', height=178.0}
        }
    }
}
```

### <font color = "#159957">打印流</font>

* PrintStream(字节打印流)和PrintWriter(字符打印流)
* 提供了一系列重载的print和println方法，用于多种数据类型的输出
* PrintStream和PrintWriter有自动flush功能，且输出不会抛出异常
* **System.out**返回的是PrintStream的实例
* 示例：

```Java
public static void main(String[] args) throws IOException{

	try(
    	FileOutputStream fos = new FileOutputStream("F:" + File.separator + "test.txt");
    	//创建打印输出流,设置为自动刷新模式(写入换行符或字节 '\n' 时都会刷新输出缓冲区)
    	PrintStream ps = new PrintStream(fos,true);
    ){
    	if (ps != null) {    // 把标准输出流(控制台输出)改成文件
        	System.setOut(ps);
        }
        for (int i = 0; i <= 255; i++) { //输出ASCII字符
        	System.out.print((char)i);
            if (i % 50 == 0) { //每50个数据一行
            	System.out.println(); // 换行
            }
        }
    }
}
```

### <font color = "#159957">回退流</font>

* PusbackInputStream 和 PushbackReader
* 可以把读取进来的数据重新退回到输入流的缓冲区中
* 除了熟悉的来自InputStream的方法外，PushbackInputStream类还提供了unread()方法：

```Java
//回推缓存已满时，如果试图回推字节，就会抛出IOException异常
void unread(int b)//回推b的低字节，这会使得后续的read()调用会把这个字节再次读取出来。
void unread(byte[] buffer)//回推buffer中的字节
void unread(byte[] buffer,int offset,int numBytes)//回推buffer中从offset开始的numBytes个字节
```

* 示例：

```Java
public static void main(String[] args) throws IOException{

	String str = "www.baidu.com" ;		// 定义字符串
    PushbackInputStream push = null ;		// 定义回退流对象
    ByteArrayInputStream bai = null ;		// 定义内存输入流
    bai = new ByteArrayInputStream(str.getBytes()) ;	// 实例化内存输入流
    push = new PushbackInputStream(bai) ;	// 从内存中读取数据
    System.out.print("读取之后的数据为：") ;
    int temp = 0 ;
    while((temp=push.read())!=-1){	// 读取内容
    	if(temp=='.'){	// 判断是否读取到了“.”
        	push.unread(temp) ;	// 放回到缓冲区之中
            temp = push.read() ;	// 再读一遍
            System.out.print("（退回"+(char)temp+"）") ;
		}else{
        	System.out.print((char)temp) ;	// 输出内容
        	//读取之后的数据为：www（退回.）baidu（退回.）com
        }
    }
}
```
