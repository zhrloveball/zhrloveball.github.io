---
title: Java多线程学习笔记(三)：使用wait()、notify()解决生产者-消费者问题
date: 2018-02-25 01:00:04
tags: 
  - 多线程
categories:
  - 笔记
---

生产者-消费者模型:
* 生产者持续生产，直到缓冲区满，阻塞；缓冲区不满后，继续生产
* 消费者持续消费，直到缓冲区空，阻塞；缓冲区不空后，继续消费
* 生产者可以有多个，消费者也可以有多个

<!-- more -->

### <font color = "#159957">产品类</font>

```Java
public class Product {

    int id;//产品ID

    public Product(int id) {
        this.id = id;
    }
}
```

### <font color = "#159957">仓库类</font>

```Java
/**
 * 仓库类，存放产品
 */
public class Repository {

    //存放产品,LinkedList实现了队列的接口
    public LinkedList<Product> store = new LinkedList( );

    public LinkedList<Product> getStore() {
        return store;
    }

    public void setStore(LinkedList<Product> store) {
        this.store = store;
    }

    /**
     * 生产产品
     * @param product 产品
     * @param threadName 线程名
     */
    public synchronized void produce(Product product, String threadName) {

        while (store.size() == 10) {
            System.out.println("仓库已满，等待消费者消费");
            try {
                //因为仓库容量已满，无法继续生产，进入等待状态，等待其他线程唤醒.
                this.wait();
            } catch (InterruptedException e) {
                e.printStackTrace( );
            }
        }
        //唤醒所有等待的消费者线程
        this.notifyAll();
        //将产品添加到仓库中.
        store.addLast(product);
        //打印生产日志
        System.out.println(threadName+"生产了:"+product.id+"号产品"+" "+"当前库存:"+store.size());
        try {
            Thread.sleep(100);
        } catch (InterruptedException e) {
            e.printStackTrace( );
        }
    }

    /**
     * 消费产品
     * @param threadName 线程名
     */
    public synchronized void consume(String threadName) {
        //当仓库没有存货时,消费者需要进行等待.等待其他线程来唤醒
        while (store.size( ) == 0) {
            System.out.println("仓库已空，等待生产者者生产");
            try {
                //因为仓库容量已空，无法继续消费，进入等待状态，等待其他线程唤醒
                this.wait();
            } catch (InterruptedException e) {
                e.printStackTrace( );
            }
        }
        //唤醒所有等待的消费者线程
        this.notifyAll();
        System.out.println(threadName+"消费了:"+store.removeFirst().id+"号产品"+" "+"当前库存:"+store.size());
        try {
            Thread.sleep(100);
        } catch (InterruptedException e) {
            e.printStackTrace( );
        }
    }
}
```

### <font color = "#159957">生产者类</font>

```Java
public class Producer implements Runnable {

    public static Integer id = 0;//产品id

    Repository repository = null;//仓库

    public Producer(Repository repository) {
        this.repository = repository;
    }

    @Override
    public void run() {
        while (true) {
            synchronized (Producer.class) {
                id++;
                Product product = new Product(id);
                repository.produce(product,Thread.currentThread().getName());
            }
        }
    }
}
```

### <font color = "#159957">消费者类</font>

```Java
public class Consumer implements Runnable{

    Repository repository=null;//仓库

    public Consumer(Repository repository) {
        this.repository=repository;
    }

    @Override
    public void run() {
        while (true) {
            repository.consume(Thread.currentThread().getName());
        }
    }
}
```

### <font color = "#159957">测试类</font>

```Java
public class Demo {

    public static void main(String[] args) {
        //一个仓库
        Repository repository = new Repository( );
        //三个生产者
        Producer p1 = new Producer(repository);
        Producer p2 = new Producer(repository);
        Producer p3 = new Producer(repository);
        //两个消费者
        Consumer c1 = new Consumer(repository);
        Consumer c2 = new Consumer(repository);
        //五个线程
        Thread t1 = new Thread(p1, "1号生产者");
        Thread t2 = new Thread(p2, "2号生产者");
        Thread t3 = new Thread(p3, "3号生产者");
        Thread t4 = new Thread(c1, "1号消费者");
        Thread t5 = new Thread(c2, "2号消费者");
        t1.start();
        t2.start();
        t3.start();
        t4.start();
        t5.start();
    }
}
```

### <font color = "#159957">结果</font>

1号生产者生产了:1号产品 当前库存:1
3号生产者生产了:2号产品 当前库存:2
1号消费者消费了:1号产品 当前库存:1
1号消费者消费了:2号产品 当前库存:0
仓库已空，等待生产者者生产
仓库已空，等待生产者者生产
3号生产者生产了:3号产品 当前库存:1
2号生产者生产了:4号产品 当前库存:2
2号消费者消费了:3号产品 当前库存:1
2号消费者消费了:4号产品 当前库存:0
仓库已空，等待生产者者生产
