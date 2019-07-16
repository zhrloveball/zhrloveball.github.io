---
title: Java流与文件学习笔记(五)：NIO简介
date: 2018-01-23 23:03:48
tags: 
  - Java IO
categories:
  - 笔记 
---

Java NIO是从Java 1.4版本开始引入的一个新的IO API，可以替代标准的Java IO API。

<!-- more -->

## <font color = "#159957">NIO与普通IO的主要区别</font>

| IO        | NIO     | 
| :-------: |:-------:| 
| 面向流    | 面向缓冲区 | 
| 阻塞IO    | 非阻塞IO   | 

在NIO中有几个比较关键的概念：Channel(通道)，Buffer(缓冲区)，Selector(选择器)。

## <font color = "#159957">通道 Channel</font>

Channel和传统IO中的Stream很相似，但又有些不同:
* 通道是双向的，既可以从通道中读取数据，又可以写数据到通道。而流通常是单向的
* 通道可以异步地读写，即是非阻塞的
* 通道中的数据总是要先读到一个Buffer，或者总是要从一个Buffer中写入

### <font color = "#159957">常用方法</font>

```Java
public interface Channel extends Closeable {

    /**
     * 判断此通道是否处于打开状态。 
     */
    public boolean isOpen();

    /**
     *关闭此通道。
     */
    public void close() throws IOException;

}
```

### <font color = "#159957">常用实现</font>

* FileChannel 从文件中读写数据
* DatagramChannel 能通过UDP读写网络中的数据
* SocketChannel 能通过TCP读写网络中的数据 
* ServerSocketChannel 可以监听新进来的TCP连接,像 Web 服务器那样。对每一个新进来的连接都会创建一个 SocketChannel

## <font color = "#159957">缓冲区 Buffer</font>

### <font color = "#159957">常用属性</font>

```Java
// Invariants: mark <= position <= limit <= capacity
private int mark = -1;
//位置 (position):下一个要读取或写入的数据的索引。缓冲区的位置不能为负
private int position = 0;
// 限制 (limit):第一个不应该读取或写入的数据的索引，即位于 limit 后的数据不可读写。缓冲区的限制不能为负，并且不能大于其容量。
private int limit;
//容量 (capacity):表示 Buffer 最大数据容量，缓冲区容量不能为负
private int capacity;
```

### <font color = "#159957">常用方法</font>

#### <font color = "#159957">向Buffer中写数据</font>

写数据到Buffer有两种方式：

```Java
//从Channel写到Buffer的例子
int bytesRead = inChannel.read(buf); //read into buffer.

//通过put方法写Buffer的例子
buf.put(127);
```

#### <font color = "#159957">从Buffer中读取数据</font>

从Buffer中读取数据有两种方式：

```Java
//从Buffer读取数据到Channel的例子：
int bytesWritten = inChannel.write(buf);

//使用get()方法从Buffer中读取数据的例子
byte aByte = buf.get();
```

#### <font color = "#159957">做标记和重置</font> 

* ``mark()`` 通过调用Buffer.mark()方法，可以标记Buffer中的一个特定position。

* ``reset()`` 通过调用Buffer.reset()方法将此缓冲区的位置重置为以前标记的位置。

#### <font color = "#159957">清除、反转和重绕</font> 

* ``clear()`` 清除此缓冲区。将位置设置为 0，将限制设置为容量，并丢弃标记。

* ``flip()`` 反转此缓冲区。首先将限制设置为当前位置，然后将位置设置为 0。如果已定义了标记，则丢弃该标记。  

* ``rewind()`` 使缓冲区为重新读取已包含的数据做好准备：它使限制保持不变，将位置设置为 0。 


### <font color = "#159957">常用实现</font>
每一个 Java 基本类型都对应着一种 Buffer，他们都包含这相同的操作，只不过是所处理的数据类型不同而已。

* ByteBuffer
* ShortBuffer
* IntBuffer
* LongBuffer
* CharBuffer
* FloatBuffer
* DoubleBuffer

## <font color = "#159957">选择器 Selector</font>

Selector是NIO的核心类，它能检测一个或多个通道 (channel) 上的事件，并将事件分发出去。使用一个 select 线程就能监听多个通道上的事件，并基于事件驱动触发相应的响应。而不需要为每个 channel 去分配一个线程。

### <font color = "#159957">SelectionKey 和 SelectableChannel</font>

* SelectableChannel
  * SelectableChannel是一类可以与Selector进行配合的通道，这类通道可以将自己感兴趣的操作（例如read、write、accept和connect）注册到一个Selector上，并在Selector的控制下进行IO相关操作。
  * 通过SelectableChannel.register()方法来实现将Channel注册到Selector上：``public final SelectionKey register(Selector sel, int ops)``

* SelectionKey
  * SelectionKey是一个用来记录SelectableChannel和Selector之间关系的对象，它由SelectableChannel的register()方法返回
  * SelectionKey的四个常量,即Selector监听的四种事件:
    * OP_READ ：等待写数据的通道可以说是“写就绪”
    * OP_WRITE ：一个有数据可读的通道可以说是“读就绪”
    * OP_CONNECT ： 成功连接到另一个服务器称为“连接就绪”
    * OP_ACCEPT ： 一个server socket channel准备好接收新进入的连接称为“接收就绪”

### <font color = "#159957">常用方法</font>

```Java
//创建Selector
public static Selector open() throws IOException;
//select方法返回你所感兴趣的事件（如连接、接受、读或写）已经准备就绪的那些通道
public abstract int select() throws IOException;//会一直阻塞，直到至少有一个注册的通道状态改变，才会被唤醒
public abstract int select(long timeout) throws IOException;//会一直阻塞，直到时间耗尽，或者有通道的状态改变
public abstract int selectNow() throws IOException;//不会阻塞，不管什么通道就绪都立刻返回
```

## <font color = "#159957">实例</font>

* NIO文件读写
```Java
public static void NIOFileCopy(String strPath, String destPath) throws IOException{

    try (
            //获取输入输出通道
            FileChannel in = new FileInputStream(new File(strPath)).getChannel();
            FileChannel out = new FileOutputStream(new File(destPath)).getChannel();
    ) {
        //创建1024字节的缓存区
        ByteBuffer buffer = ByteBuffer.allocate(1024);
        while (in.read(buffer) != -1) {
            buffer.flip();//让缓冲区将新入读的输入写入另外一个通道
            out.write(buffer);//从输出通道中将数据写入缓冲区
            buffer.clear();//重设此缓冲区，使它可以接受读入的数据
        }
    }
}
```

* NIO实现Socket通信

``服务端：``
```Java
public class NIOServer {

    /*发送数据缓冲区*/
    private ByteBuffer sendbuffer = ByteBuffer.allocate(1024);
    /*接受数据缓冲区*/
    private  ByteBuffer receivebuffer = ByteBuffer.allocate(1024);
    private Selector selector;

    /**
      * @param args
      * @throws IOException
      */
    public static void main(String[] args) throws IOException {
        int port = 8080;
        NIOServer server = new NIOServer(port);
        server.listen();
    }

    public NIOServer(int port) throws IOException {

        // 打开服务器套接字通道
        ServerSocketChannel serverSocketChannel = ServerSocketChannel.open();
        // 服务器配置为非阻塞
        serverSocketChannel.configureBlocking(false);
        // 检索与此通道关联的服务器套接字
        ServerSocket serverSocket = serverSocketChannel.socket();
        // 进行服务的绑定
        serverSocket.bind(new InetSocketAddress(port));
        // 通过open()方法找到Selector
        selector = Selector.open();
        // 注册到selector，等待连接
        serverSocketChannel.register(selector, SelectionKey.OP_ACCEPT);
        System.out.println("Server Start----:");
    }


    private void listen() throws IOException {

        while (true) {
            selector.select();
            Set<SelectionKey> selectionKeys = selector.selectedKeys();
            Iterator<SelectionKey> iterator = selectionKeys.iterator();
            while (iterator.hasNext()) {
                SelectionKey selectionKey = iterator.next();
                iterator.remove();
                handleKey(selectionKey);
            }
        }
    }

    private void handleKey(SelectionKey selectionKey) throws IOException {

        // 接受请求
        ServerSocketChannel server = null;
        SocketChannel client = null;
        String receiveText;
        String sendText;
        int count=0;

        // 测试此键的通道是否已准备好接受新的套接字连接。
        if (selectionKey.isAcceptable()) {
            // 返回为之创建此键的通道。
            server = (ServerSocketChannel) selectionKey.channel();
            // 接受到此通道套接字的连接。
            // 此方法返回的套接字通道（如果有）将处于阻塞模式。
            client = server.accept();
            // 配置为非阻塞
            client.configureBlocking(false);
            // 注册到selector，等待连接
            client.register(selector, SelectionKey.OP_READ);
        } else if (selectionKey.isReadable()) {
            // 返回为之创建此键的通道。
            client = (SocketChannel) selectionKey.channel();
            //将缓冲区清空以备下次读取
            receivebuffer.clear();
            //读取服务器发送来的数据到缓冲区中
            count = client.read(receivebuffer);
            if (count > 0) {
                receiveText = new String( receivebuffer.array(),0,count);
                System.out.println("服务器端接受客户端数据--:"+receiveText);
                client.register(selector, SelectionKey.OP_WRITE);
            }
        } else if (selectionKey.isWritable()) {
            //将缓冲区清空以备下次写入
            sendbuffer.clear();
            // 返回为之创建此键的通道。
            client = (SocketChannel) selectionKey.channel();
            sendText="message from server--";
            //向缓冲区中输入数据
            sendbuffer.put(sendText.getBytes());
            //将缓冲区各标志复位,因为向里面put了数据标志被改变要想从中读取数据发向服务器,就要复位
            sendbuffer.flip();
            //输出到通道
            client.write(sendbuffer);
            System.out.println("服务器端向客户端发送数据--："+sendText);
            client.register(selector, SelectionKey.OP_READ);
        }
    }
}
```

``客户端：``
```Java
public class NIOClient {

    /*发送数据缓冲区*/
    private static ByteBuffer sendbuffer = ByteBuffer.allocate(1024);
    /*接受数据缓冲区*/
    private static ByteBuffer receivebuffer = ByteBuffer.allocate(1024);

    public static void main(String[] args) throws IOException {
        // 打开socket通道
        SocketChannel socketChannel = SocketChannel.open();
        // 设置为非阻塞方式
        socketChannel.configureBlocking(false);
        // 打开选择器
        Selector selector = Selector.open();
        // 注册连接服务端socket动作
        socketChannel.register(selector, SelectionKey.OP_CONNECT);
        // 连接
        socketChannel.connect(new InetSocketAddress("127.0.0.1", 8080));
        Set<SelectionKey> selectionKeys;
        Iterator<SelectionKey> iterator;
        SelectionKey selectionKey;
        SocketChannel client;
        String receiveText;
        String sendText;
        int count=0;

        while (true) {

            //选择一组键，其相应的通道已为 I/O 操作准备就绪。
            //此方法执行处于阻塞模式的选择操作。
            selector.select();
            //返回此选择器的已选择键集。
            selectionKeys = selector.selectedKeys();
            iterator = selectionKeys.iterator();
            while (iterator.hasNext()) {
                selectionKey = iterator.next();
                if (selectionKey.isConnectable()) {
                    System.out.println("client connect");
                    client = (SocketChannel) selectionKey.channel();
                    // 判断此通道上是否正在进行连接操作。
                    // 完成套接字通道的连接过程。
                    if (client.isConnectionPending()) {
                        client.finishConnect();
                        System.out.println("完成连接!");
                        sendbuffer.clear();
                        sendbuffer.put("Hello,Server".getBytes());
                        sendbuffer.flip();
                        client.write(sendbuffer);
                    }
                    client.register(selector, SelectionKey.OP_READ);
                } else if (selectionKey.isReadable()) {
                    client = (SocketChannel) selectionKey.channel();
                    //将缓冲区清空以备下次读取
                    receivebuffer.clear();
                    //读取服务器发送来的数据到缓冲区中
                    count=client.read(receivebuffer);
                    if(count>0){
                        receiveText = new String( receivebuffer.array(),0,count);
                        System.out.println("客户端接受服务器端数据--:"+receiveText);
                        client.register(selector, SelectionKey.OP_WRITE);
                    }
                } else if (selectionKey.isWritable()) {
                    sendbuffer.clear();
                    client = (SocketChannel) selectionKey.channel();
                    sendText = "message from client--";
                    sendbuffer.put(sendText.getBytes());
                    //将缓冲区各标志复位,因为向里面put了数据标志被改变要想从中读取数据发向服务器,就要复位
                    sendbuffer.flip();
                    client.write(sendbuffer);
                    System.out.println("客户端向服务器端发送数据--："+sendText);
                    client.register(selector, SelectionKey.OP_READ);
                }
            }
            selectionKeys.clear();
        }
    }
}
```

## <font color = "#159957">参考文档</font>
* https://mp.weixin.qq.com/s/Vgxqvw3KufBCrHdpIEIq9Q
* http://ifeve.com/overview/




