---
title: Java多线程学习笔记(二)：多线程安全
date: 2018-02-20 20:33:52
tags: 
  - 多线程
categories:
  - 笔记
---

多线程安全问题，是由于多个线程在访问共享的数据，并且操作共享数据的语句不止一条。那么这样在多条操作共享数据的之间线程就可能发生切换。只要切换就有安全问题。

<!-- more -->

### <font color = "#159957">模拟电影院卖票</font>

```Java
/**
 * 模拟三个窗口卖100张电影票
 */
public class SellTicket extends Thread{

    //为了让多个线程共享电影票，用静态修饰
    private static int tickets = 100;

    @Override
    public void run() {
        while (true) {//模拟一直有票
            if (tickets > 0) {
                try {
                    Thread.sleep(1000);//假设每次卖票有1000毫秒延迟
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
                System.out.println(Thread.currentThread().getName() + "正在出售第" + (tickets--) + "张票");
            }
        }
    }

    public static void main(String[] args) {
        SellTicket st1 = new SellTicket();
        SellTicket st2 = new SellTicket();
        SellTicket st3 = new SellTicket();
        st1.setName("窗口1");
        st2.setName("窗口2");
        st3.setName("窗口3");
        st1.start();
        st2.start();
        st3.start();
//        结果：
//        窗口1正在出售第100张票
//        窗口3正在出售第99张票
//        ...
//        窗口1正在出售第0张票
//        窗口2正在出售第-1张票
    }
}
```
会出现出售负数票及重复票的情况。在Java中,为了保证多线程读写数据时保证数据的一致性,可以采用两种方式：
* 同步：
    1. 使用``synchronized``关键字
    2. 使用锁对象``Lock``
    3. Object中的wait方法,notify方法
    4. ``ThreadLocal``
* 使用``volatile``关键字：它能够使变量在值发生改变时能尽快地让其他线程知道。

### <font color = "#159957">synchronized</font>

java的每个对象都有一个内置锁，当用此关键字修饰方法时，内置锁会保护整个方法。在调用该方法前，需要获得内置锁，否则就处于阻塞状态。 <font color = "#FE4365">**synchronized关键字也可以修饰静态方法，此时如果调用该静态方法，将会锁住整个类。**</font>同步是一种高开销的操作，因此应该尽量减少同步的内容。 通常没有必要同步整个方法，使用synchronized代码块同步关键代码即可。

* synchronized方法

```Java
public synchronized void test() {
    System.out.println("synchronized methoed");
}
```

* synchronized代码块

```Java
public void test() {
    synchronized (this) {
        System.out.println("synchronized code block");
    }
}
```

当一个线程访问“某对象”的“synchronized方法”或者“synchronized代码块”时：
* 其他线程仍然可以访问“该对象”的非同步代码块。
* 其他线程对“该对象”的该“synchronized方法”或者“synchronized代码块”的访问将被阻塞。
* 其他线程对“该对象”的其他的“synchronized方法”或者“synchronized代码块”的访问将被阻塞。


### <font color = "#159957">synchronized和volatile</font>

* volatile本质是在告诉jvm当前变量在寄存器中的值是不确定的,需要从主存中读取,synchronized则是锁定当前变量,只有当前线程可以访问该变量,其他线程被阻塞
* <font color = "#FE4365">volatile只能修饰变量</font>，synchronized可修饰方法和代码块。
* 多线程访问<font color = "#FE4365">volatile不会发生阻塞</font>，synchronized会出现阻塞
* <font color = "#FE4365">volatile能保证数据可见性，不保证原子性</font>;synchronized可以保证原子性，也可以间接保证可见性。


### <font color = "#159957">Lock</font>

为了更清晰的表达如何加锁和释放锁，JDK5以后提供了一个新的锁对象 ``Lock``。

* 常用方法
    * 获取锁 ``void lock()``
    * 释放锁 ``void unlock()``
* 实现类
    * 重入锁 ``ReentrantLock``
* 示例
```Java
public class SellTicket extends Thread{

    private static int tickets = 100;
    private Lock lock = new ReentrantLock( );

    @Override
    public void run() {
        while (true) {
            try {
                lock.lock();//加锁
                if (tickets > 0) {
                    try {
                        Thread.sleep(1000);
                    } catch (InterruptedException e) {
                        e.printStackTrace( );
                    }
                    System.out.println(Thread.currentThread( ).getName( ) + "正在出售第" + (tickets--) + "张票");
                }
            }finally {
                lock.unlock();//释放锁
            }
        }
    }
}
```

### <font color = "#159957">死锁</font>

死锁是两个或者两个以上的线程在执行的过程中，因争夺资源产生的一种互相等待现象。同步嵌套时容易产生死锁问题。

```Java
public class DeadLock extends Thread {

    private boolean flag;
    private static final Object lockA = new Object();
    private static final Object lockB = new Object();

    public DeadLock(boolean flag) {
        this.flag = flag;
    }

    @Override
    public void run() {
        if (flag) {
            synchronized (lockA) {
                System.out.println("得到锁A，等待锁B");
                synchronized (lockB) {
                    System.out.println("得到锁A，得到锁B");
                }
            }
        } else {
            synchronized (lockB) {
                System.out.println("得到锁B，等待锁A");
                synchronized (lockA) {
                    System.out.println("得到锁B，得到锁A");
                }
            }
        }
    }

    public static void main(String[] args) {
        DeadLock deadLock1 = new DeadLock(true);
        DeadLock deadLock2 = new DeadLock(false);
        deadLock1.start();
        deadLock2.start();
    }

    //得到锁A，等待锁B
    //得到锁B，等待锁A
}
```

### <font color = "#159957">ThreadLocal</font>

多线程问题必须在以下三个条件都满足的时候才会发生：
1. 拥有多个线程
2. 共享状态
3. 该状态可变
``ThreadLocal`` 便是通过<font color = "#FE4365">避免多线程之间的状态共享</font>来规避多线程安全问题

#### <font color = "#159957">ThreadLocal源码（JDK1.6）</font>

set()方法：创建一个ThreadLocalMap，当前线程为key，要设置的对象为value。所以ThreadLocal主要目的就是<font color = "#FE4365">将变量设值到当前的线程上，以此来保证线程安全</font>。 
```Java
public void set(T value) {  
    Thread t = Thread.currentThread();  
    ThreadLocalMap map = getMap(t);  
    if (map != null)  
        map.set(this, value);  
    else  
        createMap(t, value);  
}  
```

get()方法:
```Java
public T get() {  
    Thread t = Thread.currentThread();  
    ThreadLocalMap map = getMap(t);  
    if (map != null) {  
        ThreadLocalMap.Entry e = map.getEntry(this);  
        if (e != null)  
            return (T)e.value;  
    }  
    return setInitialValue();  
}  
```

* ThreadLocal不会造成内存泄露，其中静态类ThreadLocalMap的Entry继承WeakReference
    > WeakReference一般用来防止内存泄漏，要保证内存被虚拟机回收，SoftReference多用作来实现缓存机制(cache)

