---
title: Java多线程学习笔记(三)：多线程状态
date: 2018-02-23 21:39:50
tags: 
  - 多线程
categories:
  - 笔记
---

线程从创建、运行到结束总是处于下面五个状态之一：新建状态、就绪状态、运行状态、阻塞状态及死亡状态。

<!-- more -->

### <font color = "#159957">Thread类常用API</font>

* ``public static Thread currentThread()`` 返回当前线程
* ``setName()`` 设置线程名称
    * 也可通过``Thread(String name)`` 构造方法设置线程名称
    * ``getName()`` 获取线程名称，例如``Thread.currentThread().getName()``
* ``setPriority()`` 设置线程优先级
    * ``getPriority()`` 返回线程优先级
    * 最高优先级 ``MAX_PRIORITY`` 10
    * 最低优先级 ``MIN_PRIORITY`` 1
    * 默认优先级 ``NORM_PRIORITY`` 5
    * Java线程调度是<font color = "#FE4365">抢占式调度模型</font>,优先级高仅仅表示线程<font color = "#FE4365">获取CPU时间片的概率高</font>
* 休眠线程 ``sleep()`` 线程暂停执行，以毫秒为单位
* 加入线程 ``join()`` 等待该线程终止，再执行其他线程。
* 礼让线程 ``yield()`` 暂停当前正在执行的线程对象，并执行其他线程。
    * yield()方法让**当前运行线程回到可运行状态**，使同等优先级的其他线程获得运行机会，但实际中无法保证yield()方法达到让步目的，因为让步的线程还有可能被线程调度程序再次选中。
* 守护线程 ``setDaemon()`` 将该线程标记为守护线程或用户线程。
    * 当正在运行的线程都是守护线程时，Java 虚拟机退出。 
    * 该方法必须在启动线程前调用。 
* 中断线程 ``interrupt()``
    * 一个线程不应该由其他线程来强制中断或停止，而是应该由线程自己自行停止。所以，Thread.stop, Thread.suspend,Thread.resume 都已经被废弃了
    * interrupt()方法的作用其实不是中断线程，而是<font color = "#FE4365">通知线程应该中断了</font>
    * 如果线程处于被阻塞状态（例如处于sleep, wait, join 等状态），那么线程将立即退出被阻塞状态，并抛出一个``InterruptedException``异常。如果线程处于正常活动状态，那么会将该线程的中断标志设置为 true
    

#### <font color = "#159957">sleep()和join()的区别</font>

* join(long)方法内部由wait(long)方法实现，所以join(long)具有释放锁的特点，而Thread.sleep(long)不释放锁

#### <font color = "#159957">sleep()和yield()的区别</font>

* sleep()方法让当前线程由``运行状态``进入到``休眠(阻塞)状态``，而yield()方法让当前线程由``运行状态``进入到``可运行状态``
* sleep()方法允许较低优先级的线程获得运行机会，而yield()方法执行时，当前线程仍处在可运行状态，不可能让较低优先级的线程获得CPU占有权。

### <font color = "#159957">Object类方法wait()、notify()、notifyAll()</font>

wait()的作用是让当前线程进入等待状态，同时，<font color = "#FE4365">wait()也会让当前线程释放它所持有的锁</font>。而notify()和notifyAll()的作用，则是唤醒当前对象上的等待线程；notify()是唤醒单个线程，而notifyAll()是唤醒所有的线程。

notify(), wait()依赖于"同步锁"，而"同步锁"是对象所持有，并且每个对象有且仅有一个！这就是为什么notify(), wait()等函数定义在Object类，而不是Thread类中。

#### <font color = "#159957">sleep()和wait()的区别</font>

* sleep()是Thread类的static(静态)的方法；wait()方法是Object类里的方法
* sleep()睡眠时，保持对象锁，仍然占有该锁；wait()睡眠时，释放对象锁
* 在sleep()休眠时间期满后，该线程不一定会立即执行，这是因为其它线程可能正在运行而且没有被调度为放弃执行，除非此线程具有更高的优先级；wait()使用notify或者notifyAlll或者指定睡眠时间来唤醒当前等待池中的线程
* wait()必须放在synchronized block中，否则会在runtime时扔出java.lang.IllegalMonitorStateException异常

### <font color = "#159957">线程状态</font>

>转自https://my.oschina.net/mingdongcheng/blog/139263

![](/images/article_pictures/MultiThread1.jpg)

1. 新建(new)：新创建了一个线程对象。

2. 可运行(runnable)：线程对象创建后，其他线程(比如main线程）调用了该对象的start()方法。该状态的线程位于可运行线程池中，等待被线程调度选中，获取cpu 的使用权 。

3. 运行(running)：可运行状态(runnable)的线程获得了cpu 时间片（timeslice） ，执行程序代码。

4. 阻塞(block)：阻塞状态是指线程因为某种原因放弃了cpu 使用权，也即让出了cpu timeslice，暂时停止运行。直到线程进入可运行(runnable)状态，才有机会再次获得cpu timeslice 转到运行(running)状态。阻塞的情况分三种： 
    * 等待阻塞：运行(running)的线程执行o.wait()方法，JVM会把该线程放入等待队列(waitting queue)中。

    * 同步阻塞：运行(running)的线程在获取对象的同步锁时，若该同步锁被别的线程占用，则JVM会把该线程放入锁池(lock pool)中。

    * 其他阻塞：运行(running)的线程执行Thread.sleep(long ms)或t.join()方法，或者发出了I/O请求时，JVM会把该线程置为阻塞状态。当sleep()状态超时、join()等待线程终止或者超时、或者I/O处理完毕时，线程重新转入可运行(runnable)状态。

5. 死亡(dead)：线程run()、main() 方法执行结束，或者因异常退出了run()方法，则该线程结束生命周期。死亡的线程不可再次复生。 


