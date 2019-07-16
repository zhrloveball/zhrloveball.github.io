---
title: Java流与文件学习笔记(四)：Java文件
date: 2018-01-19 21:54:36
tags: 
  - Java IO
categories:
  - 笔记
---

主要学习File类与RandomAccessFile类。File类是在java.io包中唯一与文件本身有关的，RandomAccessFile类实现了对文件内容的随机读取。

<!-- more -->


## <font color = "#159957">Java文件</font>

### <font color = "#159957">File类</font>

* File类在整个java.io包中是一个独立的类，此类的主要功能是完成与平台无关文件操作。
* 在windows和linux中路径分隔符不同，故用**File.separator**进行分割，如"F:"+File.separator+"demo.txt"
* 方法摘要
  * 创建文件：file.createNewFile();
  * 删除文件：file.delete();
  * 判断文件是否存在：file.exists();
  * 列出目录下的文件或文件夹名称：file.list();
  * 列出满足指定过滤器的文件和目录：file.list(FilenameFilter filter) ;
  * 列出全部的子文件或文件夹：file.listFiles();
  * 创建目录：file.mkdir();
  * 重命名文件：file.renameTo(File dest)；
* 示例

```Java
/**
* 列出txt文件
* @param strPath 目标路径
*/
public static String[] filteFileName(String strPath) {
	File srcFile = new File(strPath);
	String[] fileStrs = srcFile.list(new FilenameFilter() {
		@Override
		//重写过滤规则，dir：被找到的文件所在的目录。name：文件的名称。 
		public boolean accept(File dir, String name) {
			return new File(dir,name).isFile() && name.endsWith("txt");
		}
	});
	return fileStrs;
}

/**
* 重命名文件
* @param strPath 目标路径
* @param pattern 新的文件后缀
*/
public static void renameFile(String strPath,String pattern) {
	File srcFloder = new File(strPath);
	File[] files = srcFloder.listFiles();
	for (File file : files) {
		String fileName = file.getName();
        int index = fileName.lastIndexOf(".");
        String prefix = fileName.substring(0,index + 1);
        File newFile = new File(file.getParent() + File.separator + prefix + pattern);
        file.renameTo(newFile);
	}
}
```

### <font color = "#159957">RandomAccessFile类</font>

* 由于可以自由访问文件的任意位置，所以如果需要访问文件的部分内容，RandomAccessFile将是更好的选择。
* 它的父类是Object，没有继承字节流、字符流家族中任何一个类。并且它实现了DataInput、DataOutput这两个接口，既可以读也可以写。
* 重要方法：

```Java
//返回文件记录指针的当前位置
public native long getFilePointer() throws IOException;
//将文件记录指针定位到pos的位置
public native void seek(long pos) throws IOException;
```

* 示例：

```Java
public static void main(String[] args) throws IOException{

    try (
        /*
            "r"   以只读方式打开。调用结果对象的任何 write 方法都将导致抛出 IOException。
            "rw"  打开以便读取和写入。如果该文件尚不存在，则尝试创建该文件。
            "rws" 打开以便读取和写入，对于 "rw"，还要求对文件的内容或元数据的每个更新都同步写入到底层存储设备。
            "rwd" 打开以便读取和写入，对于 "rw"，还要求对文件内容的每个更新都同步写入到底层存储设备。
        */
        RandomAccessFile raf = new RandomAccessFile("F:" + File.separator + "test.txt","rw")//文件内容：helloworld
    ) {
        System.out.println("RandomAccessFile文件指针的初始位置:"+raf.getFilePointer());
        //结果：RandomAccessFile文件指针的初始位置:0

        //功能一：读取任意位置的数据
        raf.seek(5);
        byte[] bytes = new byte[1024];
        //用于保存实际读取的字节数
        int len;
        //循环读取
        while((len=raf.read(bytes))>0){
            //打印读取的内容,并将字节转为字符串输入
            System.out.println(new String(bytes,0,len));
            //结果：world
        }

        //功能二：追加数据
        raf.seek(raf.length());//将记录指针移动到文件最后
        raf.write("追加的数据".getBytes());

        //功能三：任意位置插入数据
        File tmp=File.createTempFile("test", ".tmp"); //创建一个临时文件夹来保存插入点后的数据
        tmp.deleteOnExit();//在JVM退出时删除
        FileOutputStream tmpOut = new FileOutputStream(tmp);
        FileInputStream tmpIn = new FileInputStream(tmp);

        raf.seek(5);
        byte[] buff = new byte[1024];
        int hasRead;
        while((hasRead=raf.read(buff))>0){
            tmpOut.write(buff,0,hasRead);//将插入点后的内容读入临时文件
        }

        raf.seek(5);//返回原来的插入处
        raf.write("插入的数据".getBytes());//插入需要插入的内容
        while((hasRead=tmpIn.read(buff))>0){  //最后追加临时文件中的内容
            raf.write(buff,0,hasRead);
        }

        //初始文件内容：helloworld
        //执行后文件内容：hello插入的数据world追加的数据
    }
}
```


## <font color = "#159957">参考资料</font>

* http://wangkuiwu.github.io/2012/05/01/index/
* http://www.cnblogs.com/baixl/p/4170599.html
* 《Java 核心技术 卷2》
* 《Java 语言程序设计 基础篇》
