---
layout: post
#标题配置
title: synchronized关键字详讲
#时间配置
date:   2019-07-02 13:12:00 +0800
#大类配置
categories: 多线程


---

* content
{:toc}
---
---

# synchronized

## 一句话表述
synchronized可以根据作用范围划分为对象锁和类锁，JVM会自动通过使用monitor来加锁和解锁，保证了同时只有一个线程可以执行指定代码，从而保证了线程安全，同时具有可重入和不可中断的性质。

## 不控制并发的后果
首先我们先通过一个例子来让大家感受如果在特殊情况下不使用同步方法去控制并发会产生怎样的后果。<br>
我们使用++运算所为例子，我们都知道a++运算不具有原子性，它分为三个步骤进行运算，所以下面我们将测试如果使用两个线程在不控制并发的情况下遍历执行a++操作，最终的结果会不会出现错误
```
public class BingFaTest implements Runnable{
    static BingFaTest bft=new BingFaTest();
    private static int a=0;
    @Override
    public void run() {
        for(int i=0;i<100000;i++){
            a++;
        }

    }

    public static void main(String[] args) throws InterruptedException {
        Thread thread1=new Thread(bft);
        Thread thread2=new Thread(bft);
        thread1.start();
        thread2.start();
        thread1.join();
        thread2.join();

        System.out.println(a);

    }
}
```
运行结果：
![](https://itmanmzt.github.io/styles/images/synchronized/001.jpg){:align="center"}<br><br>
我们预期的执行结果应该是200000，但最终的结果却比这个数小，这就是不控制并发的后果，可能导致最终运行结果和预期的结果不一致。

## 使用synchronized解决并发问题
synchronized关键字根据作用范围可以分为对象锁和类锁两钟，对象锁的实现方式主要用两种：1、作用于普通方法；2、通过实例对象作为锁对象作用于代码块。类锁的实现方式也有两种：1、作用于static静态方法；2、通过*.class类对象作为锁对象作用于代码块。<br>
下面我们将通过这四种方法去解决上面的并发问题<br>
作用于普通方法:
```
public class BingFaTest implements Runnable{
    static BingFaTest bft=new BingFaTest();
    private static int a=0;
    @Override
    public void run() {
       method();

    }
    public synchronized  void method(){
        for(int i=0;i<100000;i++){
            a++;
        }
    }

    public static void main(String[] args) throws InterruptedException {
        Thread thread1=new Thread(bft);
        Thread thread2=new Thread(bft);
        thread1.start();
        thread2.start();
        thread1.join();
        thread2.join();

        System.out.println("作用于普通方法"+a);

    }
}
```
运行结果：
![](https://itmanmzt.github.io/styles/images/synchronized/002.jpg){:align="center"}<br><br>
通过实例对象作为锁对象作用于代码块
```
public class BingFaTest implements Runnable{
    static BingFaTest bft=new BingFaTest();
    private static int a=0;
    @Override
    public void run() {
        synchronized (this) {
            for (int i = 0; i < 100000; i++) {
                a++;
            }
        }
    }

    public static void main(String[] args) throws InterruptedException {
        Thread thread1=new Thread(bft);
        Thread thread2=new Thread(bft);
        thread1.start();
        thread2.start();
        thread1.join();
        thread2.join();

        System.out.println("通过实例对象作为锁对象作用于代码块"+a);

    }
}
```
运行结果：
![](https://itmanmzt.github.io/styles/images/synchronized/003.jpg){:align="center"}<br><br>
作用于static静态方法
```
public class BingFaTest implements Runnable{
    static BingFaTest bft=new BingFaTest();
    private static int a=0;
    @Override
    public void run() {
       method();
    }
    public static synchronized  void method(){
        for(int i=0;i<100000;i++){
            a++;
        }
    }

    public static void main(String[] args) throws InterruptedException {
        Thread thread1=new Thread(bft);
        Thread thread2=new Thread(bft);
        thread1.start();
        thread2.start();
        thread1.join();
        thread2.join();

        System.out.println("作用于static静态方法"+a);

    }
}
```
运行结果：
![](https://itmanmzt.github.io/styles/images/synchronized/004.jpg){:align="center"}<br><br>
通过*.class类对象作为锁对象作用于代码块
```
public class BingFaTest implements Runnable{
    static BingFaTest bft=new BingFaTest();
    private static int a=0;
    @Override
    public void run() {
        synchronized (BingFaTest.class) {
            for (int i = 0; i < 100000; i++) {
                a++;
            }
        }
    }
 
    public static void main(String[] args) throws InterruptedException {
        Thread thread1=new Thread(bft);
        Thread thread2=new Thread(bft);
        thread1.start();
        thread2.start();
        thread1.join();
        thread2.join();

        System.out.println("通过*.class类对象作为锁对象作用于代码块"+a);

    }
}
```
运行结果：
![](https://itmanmzt.github.io/styles/images/synchronized/005.jpg){:align="center"}<br><br>
通过上面的代码演示我们知道对象锁和类锁都能解决并发问题，但它们的作用域大小会对其作用效果产生怎样的影响呢，下面我们再次通过代码演示。<br>
两个线程访问不同对象的使用对象锁的同步方法
```
public class BingFaTest implements Runnable{
    static BingFaTest bft1=new BingFaTest();
    static BingFaTest bft2=new BingFaTest();
    private static int a=0;
    @Override
    public void run() {
        synchronized (this) {
            for (int i = 0; i < 1000000; i++) {
                a++;
            }
        }
    }

    public static void main(String[] args) throws InterruptedException {
        Thread thread1=new Thread(bft1);
        Thread thread2=new Thread(bft2);
        thread1.start();
        thread2.start();
        thread1.join();
        thread2.join();

        System.out.println("两个线程访问不同对象的使用对象锁的同步方法"+a);

    }
}
```
运行结果：
![](https://itmanmzt.github.io/styles/images/synchronized/006.jpg){:align="center"}<br><br>
两个线程访问不同对象的使用类锁的同步方法
```
public class BingFaTest implements Runnable{
    static BingFaTest bft1=new BingFaTest();
    static BingFaTest bft2=new BingFaTest();
    private static int a=0;
    @Override
    public void run() {
        synchronized (BingFaTest.class) {
            for (int i = 0; i < 100000; i++) {
                a++;
            }
        }
    }

    public static void main(String[] args) throws InterruptedException {
        Thread thread1=new Thread(bft1);
        Thread thread2=new Thread(bft2);
        thread1.start();
        thread2.start();
        thread1.join();
        thread2.join();

        System.out.println("两个线程访问不同对象的使用类锁的同步方法"+a);

    }
}
```
运行结果：
![](https://itmanmzt.github.io/styles/images/synchronized/007.jpg){:align="center"}<br><br>
通过对比分析我们可以知道，类锁的作用范围是全局的，因为一个类的类对象只能有一个，但类的实例可以有多个。当我们需要对所有实例都进行同步时，需要选择类锁进行同步。

## synchronized的性质
synchronized具有两个重要性质，可重入性和不可中断性。可重入性是指当某个线程获取到锁之后，其内部递归调用的方法可以重复获取到这把锁，直到完全把锁释放。不可中断性事指synchronized结束的方式只有两种：抛出异常或者正常运行结束。不存在有优先级高的线程强行中断或者其他线程可以因为等待时间太长而自行退出的情况。而这些是Lock锁所具备的特性。<br>
可重入性的代码演示：<br>
自身递归：
```
public class BingFaTest implements Runnable{
    static BingFaTest bft1=new BingFaTest();
    static BingFaTest bft2=new BingFaTest();
    private int a=0;
    @Override
    public void run() {
    method();
    }
    public synchronized  void method(){
        System.out.println("可重入测试开始"+Thread.currentThread());
        try {
            Thread.sleep(3000);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        System.out.println("可重入测试结束"+Thread.currentThread());
        if(a==0) {
            a++;
            method();
        }
    }


    public static void main(String[] args) throws InterruptedException {
        Thread thread1=new Thread(bft1);
        Thread thread2=new Thread(bft1);
        thread1.start();
        thread2.start();
        thread1.join();
        thread2.join();

    }
}
```
运行结果：
![](https://itmanmzt.github.io/styles/images/synchronized/008.jpg){:align="center"}<br><br>
不同方法的调用：
```
public class BingFaTest implements Runnable{
    static BingFaTest bft1=new BingFaTest();
    static BingFaTest bft2=new BingFaTest();
    @Override
    public void run() {
    method();
    }
    public synchronized void method(){
        System.out.println("可重入测试开始"+Thread.currentThread());
        try {
            Thread.sleep(3000);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        System.out.println("可重入测试结束"+Thread.currentThread());
        method1();
    }
    public synchronized void method1(){
        System.out.println("可重入测试1开始"+Thread.currentThread());
        try {
            Thread.sleep(3000);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        System.out.println("可重入测试1结束"+Thread.currentThread());
    }


    public static void main(String[] args) throws InterruptedException {
        Thread thread1=new Thread(bft1);
        Thread thread2=new Thread(bft1);
        thread1.start();
        thread2.start();
        thread1.join();
        thread2.join();

    }
}
```
运行结果：
![](https://itmanmzt.github.io/styles/images/synchronized/009.jpg){:align="center"}<br><br>
不同类方法的调用：
```
public class BingFaTest implements Runnable{
    static BingFaTest bft1=new BingFaTest();
    static BingFaTest bft2=new BingFaTest();
    @Override
    public void run() {
        new BingFaTest1().method1();
    }
    public void method(){
        synchronized (BingFaTest.class) {
            System.out.println("可重入测试2开始" + Thread.currentThread());
            try {
                Thread.sleep(3000);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            System.out.println("可重入测试2结束" + Thread.currentThread());
        }
    }
    class BingFaTest1 extends  BingFaTest{
        public void method1(){
            synchronized (BingFaTest.class) {
                System.out.println("可重入测试1开始" + Thread.currentThread());
                try {
                    Thread.sleep(3000);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
                System.out.println("可重入测试1结束" + Thread.currentThread());
                super.method();
            }
        }
    }


    public static void main(String[] args) throws InterruptedException {
        Thread thread1=new Thread(bft1);
        Thread thread2=new Thread(bft1);
        thread1.start();
        thread2.start();
        thread1.join();
        thread2.join();

    }
}
```
运行结果：
![](https://itmanmzt.github.io/styles/images/synchronized/010.jpg){:align="center"}<br><br>

下面我们来演示一下synchronized的不可中断性，我们将对比Lock类来进行解释。顺便扩展一个特性：当synchronized抛出异常后，锁会自动释放，而Lock锁发生异常时并不会，需要自己手动释放。<br>
代码演示：<br>
synchronized类：
```
public class Thread2 implements Runnable {
    static Thread2 th=new Thread2();
    @Override
    public void run() {
        method();
    }
    public synchronized  void method(){
        System.out.println("不可中断演示"+Thread.currentThread());
        try {
            Thread.sleep(3000);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        throw new RuntimeException();

    }

    public static void main(String[] args) {
        Thread thread1=new Thread(th);
        Thread thread2=new Thread(th);
        thread1.start();
        thread2.start();
    }
}
```
运行结果：
![](https://itmanmzt.github.io/styles/images/synchronized/011.jpg){:align="center"}<br><br>
Lock类：
```
public class Thread2 implements Runnable {
    static Thread2 th=new Thread2();
    Lock lock=new ReentrantLock();
    @Override
    public void run() {
        method();
    }

   public void method(){

       lock.lock();
       System.out.println("不可中断性Lock测试"+Thread.currentThread());
       try {
           Thread.sleep(3000);
       } catch (InterruptedException e) {
           e.printStackTrace();
       }
     throw new RuntimeException();

   }

    public static void main(String[] args) {
        Thread thread1=new Thread(th);
        Thread thread2=new Thread(th);
        thread1.start();
        thread2.start();
    }
}
```
运行结果：
![](https://itmanmzt.github.io/styles/images/synchronized/012.jpg){:align="center"}<br><br>
```
public class Thread2 implements Runnable {
    static Thread2 th=new Thread2();
    Lock lock=new ReentrantLock();
    @Override
    public void run() {
        method();
    }
   public void method(){

       lock.lock();
       System.out.println("不可中断性Lock测试"+Thread.currentThread());
       try {
           Thread.sleep(3000);
       } catch (InterruptedException e) {
           e.printStackTrace();
       }
       System.out.println("不可中断性Lock测试结束"+Thread.currentThread());
      lock.unlock();

   }

    public static void main(String[] args) {
        Thread thread1=new Thread(th);
        Thread thread2=new Thread(th);
        thread1.start();
        thread2.start();
    }
}
```
运行结果：
![](https://itmanmzt.github.io/styles/images/synchronized/013.jpg){:align="center"}<br><br>
通过上面的代码演示，我们通过Lock的代码和synchronized的代码可以知道Lock可以通过unlock方法进行中断，而synchronized没有中断方法，所有它时不可中断的。并且通过在线程执行方法中抛出异常可以看出Lock抛出异常后就造成了线程死锁而synchronized不会。

## 深入探究synchronized的作用机理
首先我们先来探究synchronized加锁和释放锁的机理，其原理时通过内置锁。在每个对象的对象头都会有一个记录对应对象锁的字段，可以通过对这个字段所表示的信号进行判断改对象锁的状态。而synchronized底层也有类似与Lock加锁释放锁的方法，Java为我们提供了monitor指令，里面通过monitorEnter指令进行加锁，通过monitorExit进行释放锁操作，我们可以通过对同步方法进行反编译来进行验证。<br>
我们先编写一个同步方法类：
```
public class FanBianYiTest {
    private Object object=new Object();
    public void menthod(Thread thread){
        synchronized (object){

        }
    }
}
```
然后进行编译和反编译：<br>
![](https://itmanmzt.github.io/styles/images/synchronized/014.jpg){:align="center"}<br><br>
![](https://itmanmzt.github.io/styles/images/synchronized/015.jpg){:align="center"}<br><br>
通过观察反编译后的结果我们可以验证我们前面的解释。并且我们也可以知道一个monitorEnter可以对应多个monitorExit指令。<br><br>
下面我们将解释synchronized的可重入性原理：这类似于计算机操作系统的PV操作和临界区，存在一个信号量，我们称之为加锁计数器,初始值为0，当某个对象获取该锁时，计数器加一，也就是说每当执行一次monitorEnter指令，计数器加一，而每当执行一次monitorExit指令，计数器减一。其他的线程会根据计数器的状态来判断锁是否已经被释放而决定自身线程的状态。<br><br>
接下来我们再讲解一个synchronized可见性的原理：首先我们先来理解一下Java的内存模型，对于线程和锁对象来说，它们所用的内存是不一样的，锁对象是存储在主内存中的，而线程的信息是存储在局部内存中的，当一个线程获取锁之后，就会去主内存读取锁对象的信息并保存到自己的线程局部内存中，线程执行操作会改变内存中的信息，当要释放锁的时候，线程会将线程局部内存中的信息写到主内存中，供下一个获取锁对象的线程读取。

## synchronized的缺陷
1、效率低：锁释放情况少，试图获得锁不能设定超时，不能中断一个正在试图获得锁的线程。
2、不够灵活：加锁和释放的时机单一，每个锁仅有单一的条件，不像读写锁一样灵活，读写锁在读的时候不上锁，只有在写的时候才上锁。
3、不能知道是否成功获取锁。

## 常见问题
1、使用注意点：锁对象不能为空，作用域不宜过大，避免死锁。
2、如何选择Lock和synchronized：出于程序安全性和避免出错的原则，如果可以的话不建议使用Lock和synchronized，可以直接使用java.util.concurrent和Java.util.concurrent.atomic里面已经封装好的线程安全的类。如果要使用这些同步工具，那么非必须使用Lock特性的情况下就使用synchronized。