---
layout: post
#标题配置
title: Interrupt中断机制
#时间配置
date:   2019-08-11 10:16:00 +0800
#大类配置
categories: 多线程


---

* content
{:toc}
---
---


# Interrupt
线程的thread.interrupt()方法是中断线程，将会设置该线程的中断状态位，即设置为true，中断的结果线程是死亡、还是等待新的任务或是继续运行至下一步，就取决于这个程序本身。线程会不时地检测这个中断标示位，以判断线程是否应该被中断（中断标示值是否为true）。它并不像stop方法那样会中断一个正在运行的线程。

## 判断一个线程是否被中断
判断某个线程是否已被发送过中断请求，请使用Thread.currentThread().isInterrupted()方法（因为它将线程中断标示位设置为true后，不会立刻清除中断标示位，即不会将中断标设置为false），而不要使用thread.interrupted()（该方法调用后会将中断标示位清除，即重新设置为false）方法来判断

## 如何中断线程
1、使用中断信号量中断非阻塞的线程<br><br>
中断线程最好的，最受推荐的方式是，使用共享变量（shared variable）发出信号，告诉线程必须停止正在运行的任务。线程必须周期性的核查这一变量，然后有秩序地中止任务。
```
public class VariableThread extends Thread{
    static volatile boolean stop=false;//保证信号量的可见性

    public static void main(String[] args) {
        Thread thread=new VariableThread();
        thread.start();
        try {
            Thread.sleep(2000);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        System.out.println("start interrupt...");
        stop=true;
        System.out.println("stop interrupt...");
    }

    @Override
    public void run() {
        while(!stop){
            System.out.println("start thread...");
            try {
                Thread.sleep(1000);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
         System.out.println("stop thread...");
    }
}
```
运行结果：<br>
![](https://itmanmzt.github.io/styles/images/interrupt/001.jpg){:align="center"}<br><br><br>
2、使用interrupt中断非阻塞线程<br><br>
上面是中断一个非阻塞状态的线程的常见做法，但对非检测isInterrupted()条件会更简洁:
```
public class InterruptThread extends Thread{

    public static void main(String[] args) {
        Thread thread=new InterruptThread();
        thread.start();
        try {
            Thread.sleep(2000);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        System.out.println("start interrupt...");
        thread.interrupt();
        System.out.println("stop interrupt...");
    }

    @Override
    public void run() {
        while(!Thread.currentThread().isInterrupted()){
            System.out.println("start thread...");
            long starttime=System.currentTimeMillis();
            while(System.currentTimeMillis()-starttime<1000){

            }
        }
        System.out.println("stop thread...");
    }
}
```
运行结果：<br>
![](https://itmanmzt.github.io/styles/images/interrupt/002.jpg){:align="center"}<br><br><br>

如果线程被阻塞，它便不能核查共享变量，也就不能停止。他们可能永久的阻塞线程。即使发生超时，在超时期满之前持续等待也是不可行和不适当的，所以，要使用某种机制使得线程更早地退出被阻塞的状态。<br><br>

3、使用interrupt中断阻塞线程<br><br>
Thread.interrupt()方法不会中断一个正在运行的线程。这一方法实际上完成的是，设置线程的中断标示位，在线程受到阻塞的地方（如调用sleep、wait、join等地方）抛出一个异常InterruptedException，并且中断状态也将被清除，这样线程就得以退出阻塞的状态。<br><br>
Object.wait, Thread.sleep方法，会不断的轮询监听 interrupted 标志位，发现其设置为true后，会停止阻塞并抛出 InterruptedException异常。<br><br>
但interrupt只是设置线程的中断标示位，抛出异常后会退出阻塞状态同时将标示位置false，可以通过对异常的处理决定中不中断线程；<br><br>
不中断线程的方式：<br>
```
public class InterruptThread extends Thread{

    public static void main(String[] args) {
        Thread thread=new InterruptThread();
        thread.start();
        try {
            Thread.sleep(2000);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        System.out.println("start interrupt...");
        thread.interrupt();
        System.out.println("stop interrupt...");
    }

    @Override
    public void run() {
        while(!Thread.currentThread().isInterrupted()){
            System.out.println("start thread...");
            try {
                Thread.sleep(1000);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
        System.out.println("stop thread...");
    }
}
```
运行结果：<br>
![](https://itmanmzt.github.io/styles/images/interrupt/003.jpg){:align="center"}<br><br><br>
中断线程的方式：<br>
```
public class InterruptThread extends Thread{

    public static void main(String[] args) {
        Thread thread=new InterruptThread();
        thread.start();
        try {
            Thread.sleep(2000);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        System.out.println("start interrupt...");
        thread.interrupt();
        System.out.println("stop interrupt...");
    }

    @Override
    public void run() {
        while(!Thread.currentThread().isInterrupted()){
            System.out.println("start thread...");
            try {
                Thread.sleep(1000);
            } catch (InterruptedException e) {
                System.out.println(Thread.currentThread().isInterrupted());
                Thread.currentThread().interrupt();
            }
        }
        System.out.println("stop thread...");
    }
}
```
运行结果：<br>
![](https://itmanmzt.github.io/styles/images/interrupt/004.jpg){:align="center"}<br><br><br>
对于异常的处理方式，不要在你的底层代码里捕获InterruptedException异常后不处理，会处理不当；<br>
如果你不知道抛InterruptedException异常后如何处理，那么你有如下好的建议处理方式：<br>
1、在catch子句中，调用Thread.currentThread.interrupt()来设置中断状态（因为抛出异常后中断标示会被清除），让外界通过判断Thread.currentThread().isInterrupted()标示来决定是否终止线程还是继续下去，应该这样做：<br>
2、更好的做法就是，不使用try来捕获这样的异常，让方法直接抛出;<br><br><br>
注，synchronized在获锁的过程中是不能被中断的，意思是说如果产生了死锁，则不可能被中断（请参考后面的测试例子）。与synchronized功能相似的reentrantLock.lock()方法也是一样，它也不可中断的，即如果发生死锁，那么reentrantLock.lock()方法无法终止，如果调用时被阻塞，则它一直阻塞到它获取到锁为止。但是如果调用带超时的tryLock方法reentrantLock.tryLock(long timeout, TimeUnit unit)，那么如果线程在等待时被中断，将抛出一个InterruptedException异常，这是一个非常有用的特性，因为它允许程序打破死锁。你也可以调用reentrantLock.lockInterruptibly()方法，它就相当于一个超时设为无限的tryLock方法。<br><br>
4、死锁状态的线程没办法中断<br><br>
中断处于死锁状态的两个线程，但这两个线都没有收到任何中断信号（抛出异常），所以interrupt()方法是不能中断死锁线程的，因为锁定的位置根本无法抛出异常；<br><br>
```
public class DeadThread{
    public static void main(String[] args) {
        final Lock lock1=new ReentrantLock();
        final Lock lock2=new ReentrantLock();
        Thread thread1=new Thread(){
            @Override
            public void run() {
                method(lock1,lock2);
            }
        };
        Thread thread2=new Thread(){
            @Override
            public void run() {
                method(lock2,lock1);
            }
        };
        thread1.start();
        thread2.start();
        System.out.println("start interrupt...");
        thread1.interrupt();
        thread2.interrupt();
        System.out.println("stop interrupt....");

    }

    public static void method(Lock lock1,Lock lock2) {
        lock1.lock();
        System.out.println("lock1.lock...");
        long starttime=System.currentTimeMillis();
        while(System.currentTimeMillis()-starttime<1000){

        }
        lock2.lock();
        System.out.println("lock2.lock");
    }
}
```
运行结果：<br>
![](https://itmanmzt.github.io/styles/images/interrupt/005.jpg){:align="center"}<br><br><br>
5、中断I/O操作<br><br>
如果线程在I/O操作进行时被阻塞，又会如何？I/O操作可以阻塞线程一段相当长的时间，特别是牵扯到网络应用时。例如，服务器可能需要等待一个请求（request），又或者，一个网络应用程序可能要等待远端主机的响应。<br><br>
实现此InterruptibleChannel接口的通道是可中断的：如果某个线程在可中断通道上因调用某个阻塞的 I/O 操作（常见的操作一般有这些：serverSocketChannel. accept()、socketChannel.connect、socketChannel.open、socketChannel.read、socketChannel.write、fileChannel.read、fileChannel.write）而进入阻塞状态，而另一个线程又调用了该阻塞线程的 interrupt 方法，这将导致该通道被关闭，并且已阻塞线程接将会收到ClosedByInterruptException，并且设置已阻塞线程的中断状态。另外，如果已设置某个线程的中断状态并且它在通道上调用某个阻塞的 I/O 操作，则该通道将关闭并且该线程立即接收到 ClosedByInterruptException；并仍然设置其中断状态。<br><br>
如果你正使用通道（channels）（这是在Java 1.4中引入的新的I/O API），那么被阻塞的线程将收到一个ClosedByInterruptException异常。但是，你可能正使用Java1.0之前就存在的传统的I/O，而且要求更多的工作。既然这样，Thread.interrupt()将不起作用，因为线程将不会退出被阻塞状态。尽管interrupt()被调用，线程也不会退出被阻塞状态，比如ServerSocket的accept方法根本不抛出异常。<br><br>
很幸运，Java平台为这种情形提供了一项解决方案，即调用阻塞该线程的套接字的close()方法。在这种情形下，如果线程被I/O操作阻塞，当调用该套接字的close方法时，该线程在调用accept地方法将接收到一个SocketException（SocketException为IOException的子异常）异常，这与使用interrupt()方法引起一个InterruptedException异常被抛出非常相似，（注，如果是流因读写阻塞后，调用流的close方法也会被阻塞，根本不能调用，更不会抛IOExcepiton，此种情况下怎样中断？我想可以转换为通道来操作流可以解决，比如文件通道）。<br><br>
```
public class IOInterrupt extends Thread{
    volatile ServerSocket serverSocket;
    public static void main(String[] args) throws InterruptedException, IOException {
        IOInterrupt thread=new IOInterrupt();
        thread.start();
        Thread.sleep(1000);
        System.out.println("start interrupt...");
        thread.interrupt();
        thread.serverSocket.close();
        System.out.println("stop interrupt...");
    }

    @Override
    public void run() {
        try {
            serverSocket=new ServerSocket(8888);
        } catch (IOException e) {
            e.printStackTrace();
        }
        while (!Thread.currentThread().isInterrupted()) {
            System.out.println("start accept...");
            try {
                serverSocket.accept();
            } catch (Exception e) {
                System.out.println(Thread.currentThread().isInterrupted());
            }
        }
        System.out.println("stop accept...");
    }
}
```
运行结果：<br>
![](https://itmanmzt.github.io/styles/images/interrupt/006.jpg){:align="center"}<br><br><br>

小结：<br><br>
1、没有任何语言方面的需求一个被中断的线程应该终止。中断一个线程只是为了引起该线程的注意，被中断线程可以决定如何应对中断。

2、对于处于sleep，join等操作的线程，如果被调用interrupt()后，会抛出InterruptedException，然后线程的中断标志位会由true重置为false，因为线程为了处理异常已经重新处于就绪状态。

3、不可中断的操作，包括进入synchronized段以及Lock.lock()，inputSteam.read()等，调用interrupt()对于这几个问题无效，因为它们都不抛出中断异常。如果拿不到资源，它们会无限期阻塞下去。

对于Lock.lock()，可以改用Lock.lockInterruptibly()，可被中断的加锁操作，它可以抛出中断异常。等同于等待时间无限长的Lock.tryLock(long time, TimeUnit unit)。

对于inputStream等资源，有些(实现了interruptibleChannel接口)可以通过close()方法将资源关闭，对应的阻塞也会被放开。

<br>

<br>

<center>有Marin的地方就有你的收获</center>
