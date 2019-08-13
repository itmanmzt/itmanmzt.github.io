---
layout: post
#标题配置
title: java定时任务(线程池相关)
#时间配置
date:   2019-08-12 11:01:00 +0800
#大类配置
categories: 多线程


---

* content
{:toc}
---
---


# Timer task
在java中一个完整定时任务需要由Timer、TimerTask两个类来配合完成。 API中是这样定义他们的，Timer：一种工具，线程用其安排以后在后台线程中执行的任务。可安排任务执行一次，或者定期重复执行；TimerTask：Timer 安排的一次执行或重复执行的任务。我们可以这样理解Timer是一种定时器工具，用来在一个后台线程计划执行指定任务，而TimerTask一个抽象类，它的子类代表一个可以被Timer计划的任务。<br><br>

## Timer
在工具类Timer中，提供了四个构造方法，每个构造方法都启动了计时器线程，同时Timer类可以保证多个线程可以共享单个Timer对象而无需进行外部同步，所以Timer类是线程安全的。但是由于每一个Timer对象对应的是单个后台线程，用于顺序执行所有的计时器任务，一般情况下我们的线程任务执行所消耗的时间应该非常短，但是由于特殊情况导致某个定时器任务执行的时间太长，那么他就会“独占”计时器的任务执行线程，其后的所有线程都必须等待它执行完，这就会延迟后续任务的执行，使这些任务堆积在一起；<br><br>
构造方法：<br><br>
![](https://itmanmzt.github.io/styles/images/threadpool/006.jpg){:align="center"}<br><br>
所有方法：<br><br>
![](https://itmanmzt.github.io/styles/images/threadpool/004.jpg){:align="center"}<br><br>

## TimerTask
TimerTask类是一个抽象类，由Timer 安排为一次执行或重复执行的任务。它有一个抽象方法run()方法，该方法用于执行相应计时器任务要执行的操作。因此每一个具体的任务类都必须继承TimerTask，然后重写run()方法。
<br><br>
![](https://itmanmzt.github.io/styles/images/threadpool/007.jpg){:align="center"}<br><br>

## 具体实例(方法演示)
1、在延迟指定时间后以指定的间隔时间循环执行定时任务：schedule(TimerTask task, long delay, long period)<br><br>
```
public class TimerTest {

    public static void main(String[] args) {
        Timer timer=new Timer();
        timer.schedule(new timer(),0,1000);    
    }
}

class timer extends TimerTask{

    @Override
    public void run() {
        SimpleDateFormat simpleDateFormat=new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
        Date date=new Date();
        String str=simpleDateFormat.format(date);
        System.out.println(str);
    }
}
```
<br><br>
执行结果：<br>
![](https://itmanmzt.github.io/styles/images/threadpool/008.jpg){:align="center"}<br><br>
2、指定延时时间执行定时任务：schedule(TimerTask task, long delay)<br><br>
```
public class TimerTest {

    public static void main(String[] args) {
        Timer timer=new Timer();
        timer.schedule(new timer(),0,1000);
        timer.schedule(new myTask(),3000);
    }
}
class myTask extends TimerTask{

    @Override
    public void run() {
        System.out.println(Thread.currentThread().getName()+"  execute myTask...");
    }
}
class timer extends TimerTask{

    @Override
    public void run() {
        SimpleDateFormat simpleDateFormat=new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
        Date date=new Date();
        String str=simpleDateFormat.format(date);
        System.out.println(str);
    }
}
```
<br><br>
执行结果：<br>
![](https://itmanmzt.github.io/styles/images/threadpool/009.jpg){:align="center"}<br><br>
3、在指定时间执行定时任务：schedule(TimerTask task, Date time)<br><br>
```
public class TimerTest {

    public static void main(String[] args) {
        Date date=getTime();
        System.out.println(date);
        Timer timer=new Timer();
        timer.schedule(new timer(),0,1000);
        timer.schedule(new myTask(),date);
    }

    public static Date getTime(){
        Calendar calendar=Calendar.getInstance();
        calendar.set(Calendar.HOUR_OF_DAY,10);
        calendar.set(Calendar.MINUTE,24);
        calendar.set(Calendar.SECOND,00);
        Date time=calendar.getTime();

        return time;
    }
}
class myTask extends TimerTask{

    @Override
    public void run() {
        System.out.println(Thread.currentThread().getName()+"  execute myTask...");
    }
}
class timer extends TimerTask{

    @Override
    public void run() {
        SimpleDateFormat simpleDateFormat=new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
        Date date=new Date();
        String str=simpleDateFormat.format(date);
        System.out.println(str);
    }
}
```
<br><br>
执行结果：<br>
![](https://itmanmzt.github.io/styles/images/threadpool/010.jpg){:align="center"}<br><br>

## 方法分析
1、schedule(TimerTask task, Date time)&schedule(TimerTask task, long delay)
<br><br>
对于这两个方法而言，如果指定的计划执行时间scheduledExecutionTime<= systemCurrentTime，则task会被立即执行。scheduledExecutionTime不会因为某一个task的过度执行而改变。
<br><br>
2、schedule(TimerTask task, Date firstTime, long period)&schedule(TimerTask task, long delay, long period)
<br><br>
这两个方法与上面两个就有点儿不同的，Timer的计时器任务会因为前一个任务执行时间较长而延时。在这两个方法中，每一次执行的task的计划时间会随着前一个task的实际时间而发生改变，也就是scheduledExecutionTime(n+1)=realExecutionTime(n)+periodTime。如果第n个task由于某种情况导致这次的执行时间过程，最后导致systemCurrentTime>= scheduledExecutionTime(n+1)，这是第n+1个task并不会因为到时了而执行，他会等待第n个task执行完之后再执行，那么这样势必会导致n+2个的执行实现scheduledExecutionTime发生改变即scheduledExecutionTime(n+2) = realExecutionTime(n+1)+periodTime。所以这两个方法更加注重保存间隔时间的稳定。
<br><br>
3、scheduleAtFixedRate(TimerTask task, Date firstTime, long period)&scheduleAtFixedRate(TimerTask task, long delay, long period)
<br><br>
scheduleAtFixedRate与schedule方法的侧重点不同，schedule方法侧重保存间隔时间的稳定，而scheduleAtFixedRate方法更加侧重于保持执行频率的稳定。为什么这么说，原因如下。在schedule方法中会因为前一个任务的延迟而导致其后面的定时任务延时，而scheduleAtFixedRate方法则不会，如果第n个task执行时间过长导致systemCurrentTime>= scheduledExecutionTime(n+1)，则不会做任何等待他会立即执行第n+1个task，所以scheduleAtFixedRate方法执行时间的计算方法不同于schedule，其计算方法是scheduledExecutionTime(n)=firstExecuteTime +n*periodTime，该计算方法永远保持不变。所以scheduleAtFixedRate更加侧重于保持执行频率的稳定。<br><br>

## Timer缺陷分析
Timer计时器可以定时（指定时间执行任务）、延迟（延迟5秒执行任务）、周期性地执行任务（每隔个1秒执行任务），但是，Timer存在一些缺陷。首先Timer对调度的支持是基于绝对时间的，而不是相对时间，所以它对系统时间的改变非常敏感。其次Timer线程是不会捕获异常的，如果TimerTask抛出的了未检查异常则会导致Timer线程终止，同时Timer也不会重新恢复线程的执行，他会错误的认为整个Timer线程都会取消。同时，已经被安排但尚未执行的TimerTask也不会再执行了，新的任务也不能被调度。故如果TimerTask抛出未检查的异常，Timer将会产生无法预料的行为。
<br><br>
1、Timer管理时间延迟缺陷
<br><br>
前面Timer在执行定时任务时只会创建一个线程任务，如果存在多个线程，若其中某个线程因为某种原因而导致线程任务执行时间过长，超过了两个任务的间隔时间，会发生一些缺陷：
<br><br>
不存在线程任务执行时间过长的情况：
```
public class TimerTest {

    public static void main(String[] args) {
        Timer timer=new Timer();
        timer.schedule(new timer(),0,1000);
        timer.schedule(new myTask(),1000);
        timer.schedule(new myyTask2(),3000);
    }

}
class myTask extends TimerTask{

    @Override
    public void run() {
        System.out.println(Thread.currentThread().getName()+"  execute myTask...");
    }
}
class myyTask2 extends TimerTask{
    @Override
    public void run() {
        System.out.println(Thread.currentThread().getName()+"  execute myTask2...");
    }
}
class timer extends TimerTask{

    @Override
    public void run() {
        SimpleDateFormat simpleDateFormat=new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
        Date date=new Date();
        String str=simpleDateFormat.format(date);
        System.out.println(str);
    }
}
```
<br><br>
执行结果：<br>
![](https://itmanmzt.github.io/styles/images/threadpool/011.jpg){:align="center"}<br><br>
存在线程任务执行时间过长的情况(让线程休眠来模拟执行时间过长)：
```
public class TimerTest {

    public static void main(String[] args) {
        Timer timer=new Timer();
        timer.schedule(new timer(),0,1000);
        timer.schedule(new myTask(),1000);
        timer.schedule(new myyTask2(),3000);
    }

}
class myTask extends TimerTask{

    @Override
    public void run() {
        System.out.println(Thread.currentThread().getName()+"  execute myTask...");
        try {
            Thread.sleep(4000);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }
}
class myyTask2 extends TimerTask{
    @Override
    public void run() {
        System.out.println(Thread.currentThread().getName()+"  execute myTask2...");
    }
}
class timer extends TimerTask{

    @Override
    public void run() {
        SimpleDateFormat simpleDateFormat=new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
        Date date=new Date();
        String str=simpleDateFormat.format(date);
        System.out.println(str);
    }
}
```
<br><br>
执行结果：<br>
![](https://itmanmzt.github.io/styles/images/threadpool/012.jpg){:align="center"}<br><br>

2、Timer抛出异常缺陷
<br><br>
如果TimerTask抛出RuntimeException，Timer会终止所有任务的运行。如下：<br><br>
```
public class TimerTest {

    public static void main(String[] args) {
        Timer timer=new Timer();
        timer.schedule(new timer(),0,1000);
        timer.schedule(new myTask(),1000);
        timer.schedule(new myyTask2(),3000);
    }

}
class myTask extends TimerTask{

    @Override
    public void run() {
        System.out.println(Thread.currentThread().getName()+"  execute myTask...");
        throw new RuntimeException();
    }
}
class myyTask2 extends TimerTask{
    @Override
    public void run() {
        System.out.println(Thread.currentThread().getName()+"  execute myTask2...");
    }
}
class timer extends TimerTask{

    @Override
    public void run() {
        SimpleDateFormat simpleDateFormat=new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
        Date date=new Date();
        String str=simpleDateFormat.format(date);
        System.out.println(str);
    }
}
```
<br><br>
执行结果：<br>
![](https://itmanmzt.github.io/styles/images/threadpool/013.jpg){:align="center"}<br><br>

对于Timer的缺陷，我们可以考虑 ScheduledThreadPoolExecutor 来替代。Timer是基于绝对时间的，对系统时间比较敏感，而ScheduledThreadPoolExecutor 则是基于相对时间；Timer是内部是单一线程，而ScheduledThreadPoolExecutor内部是个线程池，所以可以支持多个任务并发执行。<br><br>

## ScheduledExecutorService
大家应该还有印象之前讲线程池的Executors线程工厂中能够创建的一种线程池newScheduledThreadPool，它的特点就是初始化的线程池可以在指定的时间内周期性的执行所提交的任务，通过追踪源码我们可以知道，其底层就是实例ScheduledThreadPoolExecutor类，而ScheduledExecutorService就是ScheduledThreadPoolExecutor的一个实现接口；<br><br>
接口定义的所有方法：<br>
![](https://itmanmzt.github.io/styles/images/threadpool/005.jpg){:align="center"}<br><br>
1、使用ScheduledExecutorService解决Timer的缺陷一：<br><br>
```
public class TimerTest {

    public static void main(String[] args) {
        ScheduledExecutorService scheduledExecutorService= Executors.newScheduledThreadPool(2);
        scheduledExecutorService.scheduleAtFixedRate(new timer(),0,1000, TimeUnit.MILLISECONDS);
        scheduledExecutorService.schedule(new myTask(),1000,TimeUnit.MILLISECONDS);
        scheduledExecutorService.schedule(new myyTask2(),3000,TimeUnit.MILLISECONDS);
    }

}
class myTask implements Runnable{

    @Override
    public void run() {
        System.out.println(Thread.currentThread().getName()+"  execute myTask...");
        try {
            Thread.sleep(4000);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }
}
class myyTask2 implements Runnable{
    @Override
    public void run() {
        System.out.println(Thread.currentThread().getName()+"  execute myTask2...");
    }
}
class timer implements Runnable{

    @Override
    public void run() {
        SimpleDateFormat simpleDateFormat=new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
        Date date=new Date();
        String str=simpleDateFormat.format(date);
        System.out.println(str);
    }
}
```
<br><br>
执行结果：<br>
![](https://itmanmzt.github.io/styles/images/threadpool/014.jpg){:align="center"}<br><br>
2、使用ScheduledExecutorService解决Timer的缺陷二：<br><br>
```
public class TimerTest {

    public static void main(String[] args) {
        ScheduledExecutorService scheduledExecutorService= Executors.newScheduledThreadPool(2);
        scheduledExecutorService.scheduleAtFixedRate(new timer(),0,1000, TimeUnit.MILLISECONDS);
        scheduledExecutorService.schedule(new myTask(),1000,TimeUnit.MILLISECONDS);
        scheduledExecutorService.schedule(new myyTask2(),3000,TimeUnit.MILLISECONDS);
    }

}
class myTask implements Runnable{

    @Override
    public void run() {
        System.out.println(Thread.currentThread().getName()+"  execute myTask...");
        throw new RuntimeException();
    }
}
class myyTask2 implements Runnable{
    @Override
    public void run() {
        System.out.println(Thread.currentThread().getName()+"  execute myTask2...");
    }
}
class timer implements Runnable{

    @Override
    public void run() {
        SimpleDateFormat simpleDateFormat=new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
        Date date=new Date();
        String str=simpleDateFormat.format(date);
        System.out.println(str);
    }
}
```
<br><br>
执行结果：<br>
![](https://itmanmzt.github.io/styles/images/threadpool/015.jpg){:align="center"}<br><br>

<br>

<br>

<center>有Marin的地方就有你的收获</center>