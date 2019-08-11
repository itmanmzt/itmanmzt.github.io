---
layout: post
#标题配置
title: 线程优先级和守护线程
#时间配置
date:   2019-08-11 11:19:00 +0800
#大类配置
categories: 多线程


---

* content
{:toc}
---
---

# Thread Priority & Daemon Thread
java 中的线程优先级的范围是1～10，默认的优先级是5。“高优先级线程”会优先于“低优先级线程”执行。
<br><br>
java 中有两种线程：用户线程和守护线程。可以通过isDaemon()方法来区别它们：如果返回false，则说明该线程是“用户线程”；否则就是“守护线程”。<br><br>
用户线程一般用户执行用户级任务，而守护线程也就是“后台线程”，一般用来执行后台任务，并为前台线程服务。需要注意的是：守护线程会跟随着Java虚拟机在“用户线程”都结束后会后退出。<br><br>
在守护线程中产生的新线程也是守护线程；<br><br>
不是所有的任务都可以分配给守护线程来执行，比如读写操作或计算逻辑；<br><br>
守护线程的常见应用：<br>
1、数据库连接池中的监测线程;<br>
2、JVM虚拟机启动后的监测线程;<br>
3、垃圾回收线程；<br><br>

注：可以通过调用Thread的setDaemon(true)来设置当前线程为守护线程，但需要注意的是，设置守护线程必须在线程启动之前调用，否则会抛出IllegalThreadStateException异常；<br><br>

JDK中关于线程优先级和守护线程的介绍：<br>
```
每个线程都有一个优先级。“高优先级线程”会优先于“低优先级线程”执行。每个线程都可以被标记为一个守护进程或非守护进程。在一些运行的主线程中创建新的子线程时，子线程的优先级被设置为等于“创建它的主线程的优先级”，当且仅当“创建它的主线程是守护线程”时“子线程才会是守护线程”。

当Java虚拟机启动时，通常有一个单一的非守护线程（该线程通过是通过main()方法启动）。JVM会一直运行直到下面的任意一个条件发生，JVM就会终止运行：
(01) 调用了exit()方法，并且exit()有权限被正常执行。
(02) 所有的“非守护线程”都死了(即JVM中仅仅只有“守护线程”)。

每一个线程都被标记为“守护线程”或“用户线程”。当只有守护线程运行时，JVM会自动退出。
```

# 线程优先级实例
```
public class PriorityThread implements Runnable{
    ThreadLocal<Integer> threadLocal=new ThreadLocal<Integer>(){
        @Override
        protected Integer initialValue() {
            return 0;
        }
    };
    long startTime=System.currentTimeMillis();
    public static void main(String[] args) {
        PriorityThread runnable=new PriorityThread();
        Thread thread=new Thread(runnable);
        Thread thread1=new Thread(runnable);
        thread.setPriority(1);
        thread1.setPriority(5);
        thread.start();
        thread1.start();

    }

    @Override
    public void run() {
        while(System.currentTimeMillis()-startTime<2000) {
            synchronized (this) {
                System.out.println(Thread.currentThread().getName() + "Start..");
                threadLocal.set(threadLocal.get()+1);
                System.out.println(Thread.currentThread().getName()+"执行了"+threadLocal.get());
            }
        }
    }
}
```
运行结果：<br>
![](https://itmanmzt.github.io/styles/images/interrupt/007.jpg){:align="center"}<br><br>

## 守护线程实例
```
public class DaemonThread {
    public static void main(String[] args) {
        UserThread userThread=new UserThread();
        DaemonThread1 daemonThread=new DaemonThread1();
        daemonThread.setDaemon(true);
        userThread.start();
        daemonThread.start();
        try {
            Thread.sleep(1000);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        userThread.stop();//stop方法是不安全的，这里只是方便演示
    }

}

class UserThread extends Thread{
    @Override
    public void run() {
        while(true) {
            System.out.println("UserThread running...");
            try {
                Thread.sleep(200);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
    }
}

class DaemonThread1 extends Thread{
    @Override
    public void run() {
        while(true) {
            System.out.println("DaemonThread running...");
            try {
                Thread.sleep(200);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
    }
}
```
运行结果：<br>
![](https://itmanmzt.github.io/styles/images/interrupt/008.jpg){:align="center"}<br><br>
当主线程执行完毕，我们又中断了用户线程，也就是说这个时候只剩下守护线程在运行，所以守护线程会跟随着Java虚拟机在“用户线程”都结束后会后退出；

<br>

<br>

<center>有Marin的地方就有你的收获</center>