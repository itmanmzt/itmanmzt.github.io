---
layout: post
#标题配置
title: 线程的状态图
#时间配置
date:   2019-08-11 12:54:00 +0800
#大类配置
categories: 多线程


---

* content
{:toc}
---
---

# Thread State

## 从线程状态图看线程状态
![](https://itmanmzt.github.io/styles/images/threadState/001.jpg){:align="center"}<br><br>

线程共包括以下5种状态。<br>

新建状态(New)：线程对象被创建后，就进入了新建状态。例如，Thread thread = new Thread()。<br>

就绪状态(Runnable)： 也被称为“可执行状态”。线程对象被创建后，其它线程调用了该对象的start()方法，从而来启动该线程。例如，thread.start()。处于就绪状态的线程，随时可能被CPU调度执行。<br>

运行状态(Running) : 线程获取CPU权限进行执行。需要注意的是，线程只能从就绪状态进入到运行状态。<br>

阻塞状态(Blocked)： 阻塞状态是线程因为某种原因放弃CPU使用权，暂时停止运行。直到线程进入就绪状态，才有机会转到运行状态。阻塞的情况分三种： <br>
(01) 等待阻塞 – 通过调用对象的wait()方法，让线程等待notify()/notifyAll()被调用。 <br>
(02) 同步阻塞 – 线程在获取synchronized同步锁失败（因为锁被其它线程所占用），它会进入同步阻塞状态。<br> 
(03) 其他阻塞 – 通过调用线程的sleep()或join()或发出了I/O请求时，线程会进入到其他阻塞状态。当sleep()状态超时、join()等待线程终止或者超时、或者I/O处理完毕时，线程重新转入就绪状态。<br>

死亡状态(Dead)：线程执行完毕或者因异常退出了run()方法，该线程结束生命周期。<br>

## 从Thread源码看线程状态
```
public enum State {
        /**
         * Thread state for a thread which has not yet started.
         */
        NEW,

        /**
         * Thread state for a runnable thread.  A thread in the runnable
         * state is executing in the Java virtual machine but it may
         * be waiting for other resources from the operating system
         * such as processor.
         */
        RUNNABLE,

        /**
         * Thread state for a thread blocked waiting for a monitor lock.
         * A thread in the blocked state is waiting for a monitor lock
         * to enter a synchronized block/method or
         * reenter a synchronized block/method after calling
         * {@link Object#wait() Object.wait}.
         */
        BLOCKED,

        /**
         * Thread state for a waiting thread.
         * A thread is in the waiting state due to calling one of the
         * following methods:
         * <ul>
         *   <li>{@link Object#wait() Object.wait} with no timeout</li>
         *   <li>{@link #join() Thread.join} with no timeout</li>
         *   <li>{@link LockSupport#park() LockSupport.park}</li>
         * </ul>
         *
         * <p>A thread in the waiting state is waiting for another thread to
         * perform a particular action.
         *
         * For example, a thread that has called <tt>Object.wait()</tt>
         * on an object is waiting for another thread to call
         * <tt>Object.notify()</tt> or <tt>Object.notifyAll()</tt> on
         * that object. A thread that has called <tt>Thread.join()</tt>
         * is waiting for a specified thread to terminate.
         */
        WAITING,

        /**
         * Thread state for a waiting thread with a specified waiting time.
         * A thread is in the timed waiting state due to calling one of
         * the following methods with a specified positive waiting time:
         * <ul>
         *   <li>{@link #sleep Thread.sleep}</li>
         *   <li>{@link Object#wait(long) Object.wait} with timeout</li>
         *   <li>{@link #join(long) Thread.join} with timeout</li>
         *   <li>{@link LockSupport#parkNanos LockSupport.parkNanos}</li>
         *   <li>{@link LockSupport#parkUntil LockSupport.parkUntil}</li>
         * </ul>
         */
        TIMED_WAITING,

        /**
         * Thread state for a terminated thread.
         * The thread has completed execution.
         */
        TERMINATED;
    }
```
从源码我们可以看出JDK为我们定义的线程状态为6种，分别是NEW、RUNNABLE、BLOCKED、WAITING、TIMED_WAITING、TERMINATED；<br><br>
JVM中的线程状态图：<br>
![](https://itmanmzt.github.io/styles/images/threadState/002.jpg){:align="center"}<br><br>

## 从操作系统看线程状态
![](https://itmanmzt.github.io/styles/images/threadState/003.jpg){:align="center"}<br><br>

在现在的操作系统中，因为线程依旧被视为轻量级进程，所以操作系统中线程的状态实际上和进程状态是一致的模型。<br><br>

从实际意义上来讲，操作系统中的线程除去new和terminated状态，一个线程真实存在的状态，只有：<br><br>
ready：表示线程已经被创建，正在等待系统调度分配CPU使用权。<br>
running：表示线程获得了CPU使用权，正在进行运算<br>
waiting：表示线程等待（或者说挂起），让出CPU资源给其他线程使用<br><br>
为什么除去new和terminated状态？是因为这两种状态实际上并不存在于线程运行中，所以也没什么实际讨论的意义。<br><br>
对于Java中的线程状态：<br><br>

无论是Timed Waiting ，Waiting还是Blocked，对应的都是操作系统线程的**waiting（等待**）状态。<br>
而Runnable状态，则对应了操作系统中的ready和running状态。<br><br>

<br>

<br>

<center>有Marin的地方就有你的收获</center>

