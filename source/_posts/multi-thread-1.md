---
title: Java多线程学习笔记(一)：多线程基础
date: 2018-02-07 20:16:04
tags: 
  - 多线程
categories:
  - 笔记
---

> 对于多线程，Java提供了丰富且强健的支持。在源代码方面，Java提供了synchronized关键字，对具体一个对象实现线程独占，完成所谓的原子操作。在程序方面，Java提供的多线程支持主要体现在Object和Thread两个类上，可以看出，Java从开始就视每一个类实例是生存在多线程的环境当中的。

<!-- more -->

### <font color = "#159957">基本概念</font>

#### <font color = "#159957">进程与线程</font>

* 进程是系统进行资源分配和调用的最小单位，线程是程序执行的最小单位，一个进程可以由多个线程组成
* 进程有自己的独立地址空间，而<font color = "#FE4365">线程是共享进程中的数据</font>的，使用相同的地址空间。
* 多线程程序只要有一个线程死掉，整个进程也死掉了，而一个进程死掉并不会对另外一个进程造成影响，因为<font color = "#FE4365">进程有自己独立的内存空间和系统资源</font>。

#### <font color = "#159957">并行与并发</font>

* 并行是逻辑上同时发生，指在某一个<font color = "#FE4365">时间内</font>同时运行多个程序。
* 并发是物理上同时发生，指在某一个<font color = "#FE4365">时间点</font>同时运行多个程序。

### <font color = "#159957">多线程的意义</font>

* 程序的执行其实就是在抢CPU的执行权，多线程是为了提高CPU的使用率。
    * 但是创建太多线程，CPU 花费在上下文的切换的时间将多于执行程序的时间。
* JVM启动至少启动了垃圾回收线程和主线程，所以是多线程的。

### <font color = "#159957">实现多线程的两种方法</font>

#### <font color = "#159957">继承Thread类</font>

1. 自定义类MyThread继承Thread类
2. 重写父类的run()方法
3. 创建对象
4. 启动线程 

```Java
public class MyThread extends Thread {

    @Override
    public void run() {
        System.out.println(Thread.currentThread().getName());
    }

    public static void main(String[] args) {
        MyThread my = new MyThread();
        my.start();
        System.out.println(Thread.currentThread().getName());
    }
}
//结果
//main
//Thread-0
```

#### <font color = "#159957">实现Runnable接口</font>

实现Runnable接口和继承自Thread类差不多，需要用MyThread对象作为构造参数创建Thread类的对象。

```Java
public class MyThread implements Runnable{

    @Override
    public void run() {
        System.out.println(Thread.currentThread().getName());
    }

    public static void main(String[] args) {

        MyThread my = new MyThread();
        Thread thread = new Thread(my);
        thread.start();
        System.out.println(Thread.currentThread().getName());
    }
}
```

#### <font color = "#159957">匿名内部类实现多线程</font>

```Java
public class AnonymousInnerClass {

    public static void main(String[] args) {
        //继承Thread类
        new Thread(){
            public void run(){
                for (int x=0;x<100;x++) {
                    System.out.println(Thread.currentThread( ).getName( ) + ":" + x);
                }
            }
        }.start();
        
        //实现Runnable接口
        new Thread(new Runnable( ) {
            @Override
            public void run() {
                for (int x=0;x<100;x++) {
                    System.out.println(Thread.currentThread( ).getName( ) + ":" + x);
                }
            }
        }){}.start();
    }
}
```

#### <font color = "#159957">两种方法比较</font>

* Thread类其实也是实现的Runnable接口
* 实现Runnable接口可以避免单继承的局限性、减少耦合
* 实现Runnable接口适合多个程序处理同一资源的情况，将线程与代码、数据有效分离，体现面向对象思想
    * 因为若继承Thread类，创建两个线程时需创建两次
        ```Java
        MyThread my1 = new MyThread();
        MyThread my2 = new MyThread();
        my1.start();
        my2.start();
        ```
    * 而实现Runnable接口，创建两个线程时只需创建一次该类
        ```Java
        MyRunnable my = new MyRunnable();
        Thread t1 = new Thread(my,"线程1");
        Thread t2 = new Thread(my,"线程2");
        t1.start();
        t2.start();
        ```


### <font color = "#159957">run()和start()</font>

* ``start()`` 
    * <font color = "#FE4365">启动一个新线程,此线程处于就绪（可运行）状态，并没有运行，一旦得到cpu时间片，JVM就开始执行run()方法</font>;
    * 当同一线程调用两次start()时，会抛出IllegalThreadStateException异常
* ``run()`` 
    * 普通成员方法，仅仅是封装被线程执行的方法；
    * 单独调用不会启动新线程；可重复调用

``Thread.start()`` 源码:
```Java
public synchronized void start() {

    //当线程状态不是NEW时，会抛出异常
    if (threadStatus != 0)
        throw new IllegalThreadStateException();

    //通知线程组 线程即将启动
    group.add(this);

    boolean started = false;
    try {
        //通过本地方法start0()启动线程:private native void start0();
        start0();
        started = true;
    } finally {
        try {
            if (!started) {
                group.threadStartFailed(this);
            }
        } catch (Throwable ignore) {

        }
    }
}
```
``Thread.run()`` 源码:
```Java
private Runnable target;

@Override
public void run() {
    //调用Runnable成员的run()方法，不会创建新线程
    if (target != null) {
        target.run();
    }
}
```


