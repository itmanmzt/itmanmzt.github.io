---
layout: post
#标题配置
title: ThreadLocal关键词详讲
#时间配置
date:   2019-08-09 15:14:00 +0800
#大类配置
categories: 多线程


---

* content
{:toc}
---
---

# ThreadLocal
ThreadLocal，很多地方叫做线程本地变量，也有些地方叫做线程本地存储，其实意思差不多。可能很多朋友都知道ThreadLocal为变量在每个线程中都创建了一个副本，那么每个线程可以访问自己内部的副本变量。<br><br>
ThreadLocal 的作用和同步机制有些相反：同步机制是为了保证多线程环境下数据的一致性；而 ThreadLocal 是保证了多线程环境下数据的独立性。<br><br>

## ThreadLocal源码分析
分析重点：
```
public T get() { }
public void set(T value) { }
public void remove() { }
protected T initialValue() { }
static class ThreadLocalMap { }
void createMap(Thread t, T firstValue) { }
ThreadLocal.ThreadLocalMap threadLocals;
```
get()方法是用来获取ThreadLocal在当前线程中保存的变量副本，set()用来设置当前线程中变量的副本，remove()用来移除当前线程中变量的副本，initialValue()是一个protected方法，一般是用来在使用时进行重写的，它是一个延迟加载方法，ThreadLocalMap是内部类，用来保存变量副本；<br><br>
ThreadLocal类为每个线程创建一个变量的副本集合（threadLocals),我们可以通过threadLocal调用上面的方法来操作其对应的变量副本；<br><br>
get方法：
```
/**
     * Returns the value in the current thread's copy of this
     * thread-local variable.  If the variable has no value for the
     * current thread, it is first initialized to the value returned
     * by an invocation of the {@link #initialValue} method.
     *
     * @return the current thread's value of this thread-local
     */
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
该方法会获取当前线程，然后通过getMap方法去获取当前线程的变量的副本集合，然后从集合从获取对应threadLocal的副本变量值，这里获取键值传入的this指的就是调用get方法的threadLocal实例；如果集合中不存在对应的副本变量，则进行延迟加载；延迟加载的方法需要我们重写，所以在没有重写initialValue方法的情况下，需要先调用set方法再调用get方法，不然所以线程都只会返回null，具体原因后面将进行分析和实例展示；<br><br>
set方法：
```
 /**
     * Sets the current thread's copy of this thread-local variable
     * to the specified value.  Most subclasses will have no need to
     * override this method, relying solely on the {@link #initialValue}
     * method to set the values of thread-locals.
     *
     * @param value the value to be stored in the current thread's copy of
     *        this thread-local.
     */
    public void set(T value) {
        Thread t = Thread.currentThread();
        ThreadLocalMap map = getMap(t);
        if (map != null)
            map.set(this, value);
        else
            createMap(t, value);
    }
```
set方法会对map集合进行初始化并赋值；<br><br>
remove方法：
```
/**
     * Removes the current thread's value for this thread-local
     * variable.  If this thread-local variable is subsequently
     * {@linkplain #get read} by the current thread, its value will be
     * reinitialized by invoking its {@link #initialValue} method,
     * unless its value is {@linkplain #set set} by the current thread
     * in the interim.  This may result in multiple invocations of the
     * <tt>initialValue</tt> method in the current thread.
     *
     * @since 1.5
     */
     public void remove() {
         ThreadLocalMap m = getMap(Thread.currentThread());
         if (m != null)
             m.remove(this);
     }
```
remove方法会从map集合中删除对应的threadLocal变量副本；<br><br>
initialValue方法：
```
 /**
     * Returns the current thread's "initial value" for this
     * thread-local variable.  This method will be invoked the first
     * time a thread accesses the variable with the {@link #get}
     * method, unless the thread previously invoked the {@link #set}
     * method, in which case the <tt>initialValue</tt> method will not
     * be invoked for the thread.  Normally, this method is invoked at
     * most once per thread, but it may be invoked again in case of
     * subsequent invocations of {@link #remove} followed by {@link #get}.
     *
     * <p>This implementation simply returns <tt>null</tt>; if the
     * programmer desires thread-local variables to have an initial
     * value other than <tt>null</tt>, <tt>ThreadLocal</tt> must be
     * subclassed, and this method overridden.  Typically, an
     * anonymous inner class will be used.
     *
     * @return the initial value for this thread-local
     */
    protected T initialValue() {
        return null;
    }
```
initialValue方法默认返回的是null，在实际使用中需要我们重写该方法，这也是我们前面提到在调用get方法之前没有调用set方法也没有重写initialValue方法会所有线程都返回null的原因，下面我们结合源码来分析：<br><br>
首先我们知道直接调用get方法的话，map中不存在副本变量，所以就会调用setInitialValue方法：
```
/**
     * Variant of set() to establish initialValue. Used instead
     * of set() in case user has overridden the set() method.
     *
     * @return the initial value
     */
    private T setInitialValue() {
        T value = initialValue();
        Thread t = Thread.currentThread();
        ThreadLocalMap map = getMap(t);
        if (map != null)
            map.set(this, value);
        else
            createMap(t, value);
        return value;
    }
```
此时T value = initialValue()调用的返回值为null,也就是返回的value为null,所以所有的get方法的返回值都为nul;<br><br>

调用get方法之前没有调用set方法也没有重写initialValue方法的实例代码：
```
public class ThreadLocalTest {
    ThreadLocal<Long> longThreadLocal=new ThreadLocal<Long>();
    ThreadLocal<String> stringThreadLocal=new ThreadLocal<String>();
    public static void main(String[] args) throws InterruptedException {

        final ThreadLocalTest test=new ThreadLocalTest();
        System.out.println(test.longThreadLocal.get());
        System.out.println(test.stringThreadLocal.get());

        Thread thread=new Thread(new Runnable() {
            @Override
            public void run() {
                System.out.println(test.longThreadLocal.get());
                try {
                    Thread.sleep(1000);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
                System.out.println(test.stringThreadLocal.get());

            }
        });
        thread.start();
        thread.join();
        System.out.println(test.longThreadLocal.get());
        System.out.println(test.stringThreadLocal.get());
    }
}
```
运行结果：<br>
![](https://itmanmzt.github.io/styles/images/threadlocal/001.jpg){:align="center"}<br><br>
重写initialValue的实例代码：
```
public class ThreadLocalTest {
    ThreadLocal<Long> longThreadLocal=new ThreadLocal<Long>(){
        @Override
        protected Long initialValue() {
            return Thread.currentThread().getId();
        }
    };
    ThreadLocal<String> stringThreadLocal=new ThreadLocal<String>(){
        @Override
        protected String initialValue() {
            return Thread.currentThread().getName();
        }
    };
    public static void main(String[] args) throws InterruptedException {

        final ThreadLocalTest test=new ThreadLocalTest();
        System.out.println(test.longThreadLocal.get());
        System.out.println(test.stringThreadLocal.get());

        Thread thread=new Thread(new Runnable() {
            @Override
            public void run() {
                System.out.println(test.longThreadLocal.get());
                try {
                    Thread.sleep(1000);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
                System.out.println(test.stringThreadLocal.get());

            }
        });
        thread.start();
        thread.join();
        System.out.println(test.longThreadLocal.get());
        System.out.println(test.stringThreadLocal.get());
    }
}
```
运行结果：<br>
![](https://itmanmzt.github.io/styles/images/threadlocal/002.jpg){:align="center"}<br><br>

## ThreadLocal的内存泄露问题
threadlocal里面使用了一个存在弱引用的map,当释放掉threadlocal的强引用以后,map里面的value却没有被回收.而这块value永远不会被访问到了. 所以存在着内存泄露. 最好的做法是将调用threadlocal的remove方法。<br><br>
```
static class ThreadLocalMap {

        /**
         * The entries in this hash map extend WeakReference, using
         * its main ref field as the key (which is always a
         * ThreadLocal object).  Note that null keys (i.e. entry.get()
         * == null) mean that the key is no longer referenced, so the
         * entry can be expunged from table.  Such entries are referred to
         * as "stale entries" in the code that follows.
         */
        static class Entry extends WeakReference<ThreadLocal<?>> {
            /** The value associated with this ThreadLocal. */
            Object value;

            Entry(ThreadLocal<?> k, Object v) {
                super(k);
                value = v;
            }
        }
}
```
在threadlocal的生命周期中,都存在这些引用. 看下图: 实线代表强引用,虚线代表弱引用。<br><br>
![](https://itmanmzt.github.io/styles/images/threadlocal/003.jpg){:align="center"}<br><br>
如上图，ThreadLocalMap使用ThreadLocal的弱引用作为key，如果一个ThreadLocal没有外部强引用引用他，那么系统gc的时候，这个ThreadLocal势必会被回收，这样一来，ThreadLocalMap中就会出现key为null的Entry，就没有办法访问这些key为null的Entry的value，这些key为null的Entry的value就会一直存在一条强引用链：ThreadLocal Ref -> Thread -> ThreaLocalMap -> Entry -> value ；这就是我们认为的内存泄露，只有当前thread结束以后, current thread就不会存在栈中,强引用断开, Current Thread, Map, value将全部被GC回收。<br><br>
```
 private Entry getEntry(ThreadLocal<?> key) {
            int i = key.threadLocalHashCode & (table.length - 1);
            Entry e = table[i];
            if (e != null && e.get() == key)
                return e;
            else
                return getEntryAfterMiss(key, i, e);
        }
```
```
 private void set(ThreadLocal key, Object value) {

            // We don't use a fast path as with get() because it is at
            // least as common to use set() to create new entries as
            // it is to replace existing ones, in which case, a fast
            // path would fail more often than not.

            Entry[] tab = table;
            int len = tab.length;
            int i = key.threadLocalHashCode & (len-1);

            for (Entry e = tab[i];
                 e != null;
                 e = tab[i = nextIndex(i, len)]) {
                ThreadLocal k = e.get();

                if (k == key) {
                    e.value = value;
                    return;
                }

                if (k == null) {
                    replaceStaleEntry(key, value, i);
                    return;
                }
            }

            tab[i] = new Entry(key, value);
            int sz = ++size;
            if (!cleanSomeSlots(i, sz) && sz >= threshold)
                rehash();
        }
```
Java为了最小化减少内存泄露的可能性和影响，在ThreadLocal的get,set的时候都会清除线程Map里所有key为null的value。但是还是会出现这种情况：threadLocal对象设null了，开始发生“内存泄露”，然后使用线程池，这个线程结束，线程放回线程池中不销毁，这个线程一直不被使用，或者分配使用了又不再调用get,set方法，那么这个期间就会发生真正的内存泄露。<br><br>
很多情况下需要使用者手动调用ThreadLocal的remove函数，手动删除不再需要的ThreadLocal，防止内存泄露。所以JDK建议将ThreadLocal变量定义成private static的，这样的话ThreadLocal的生命周期就更长，由于一直存在ThreadLocal的强引用，所以ThreadLocal也就不会被回收，也就能保证任何时候都能根据ThreadLocal的弱引用访问到Entry的value值，然后remove它，防止内存泄露。<br><br>
<br>

<br>

<center>有Marin的地方就有你的收获</center>