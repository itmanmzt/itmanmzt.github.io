---
layout: post
#标题配置
title: 线程池的原理和Executor框架
#时间配置
date:   2019-08-11 20:48:00 +0800
#大类配置
categories: 多线程


---

* content
{:toc}
---
---

# Thread Pool


## 关于线程池一些比较重要的类或接口
(1)ExecutorService是真正的线程池接口，所以我们在通过Excutors创建各种线程时，一般采用如下代码：ExecutorService threadPool = Executors.newXXX();<br><br>
(2)Executors是静态工厂，生产各种类型线程池。<br><br>
(3)Executor与Executors，Executor是线程池的顶级接口，但它只是一个执行线程的工具，真正的线程池接口是ExecutorService。<br><br>
(4)AbstractExecutorService实现了ExecutorService接口，实现了其中大部分的方法（有没有实现的，所以被声明为Abstract）。<br><br>
(5)ThreadPoolExecutor，继承了AbstractExecutorService，是ExecutorService的默认实现，也是一会将要介绍的重点。<br><br>
这五个类或接口实现了从线程池顶层接口到底层实现的整个架构。<br><br>
![](https://itmanmzt.github.io/styles/images/threadpool/002.jpg){:align="center"}<br><br>

Executors创建的各种线程池最终就是创建ThreadPoolExecutor实例<br><br>
(1)SingleThreadExecutor<br>
特点：单线程串行工作，如果这个唯一的线程因为异常终止，则有一个新的线程来替代它。内部使用LinkedBlockingQueue作为阻塞队列。<br>
```
/**
     * Creates an Executor that uses a single worker thread operating
     * off an unbounded queue. (Note however that if this single
     * thread terminates due to a failure during execution prior to
     * shutdown, a new one will take its place if needed to execute
     * subsequent tasks.)  Tasks are guaranteed to execute
     * sequentially, and no more than one task will be active at any
     * given time. Unlike the otherwise equivalent
     * <tt>newFixedThreadPool(1)</tt> the returned executor is
     * guaranteed not to be reconfigurable to use additional threads.
     *
     * @return the newly created single-threaded Executor
     */
    public static ExecutorService newSingleThreadExecutor() {
        return new FinalizableDelegatedExecutorService
            (new ThreadPoolExecutor(1, 1,
                                    0L, TimeUnit.MILLISECONDS,
                                    new LinkedBlockingQueue<Runnable>()));
    }
```
<br><br>
(2)FixedThreadPool<br>
特点：固定大小的线程池，如果设定的所有线程都在运行，新任务会在任务队列等待。使用LinkedBlockingQuene作为阻塞队列，不过当线程池没有可执行任务时，也不会释放线程。<br>
```
/**
     * Creates a thread pool that reuses a fixed number of threads
     * operating off a shared unbounded queue.  At any point, at most
     * <tt>nThreads</tt> threads will be active processing tasks.
     * If additional tasks are submitted when all threads are active,
     * they will wait in the queue until a thread is available.
     * If any thread terminates due to a failure during execution
     * prior to shutdown, a new one will take its place if needed to
     * execute subsequent tasks.  The threads in the pool will exist
     * until it is explicitly {@link ExecutorService#shutdown shutdown}.
     *
     * @param nThreads the number of threads in the pool
     * @return the newly created thread pool
     * @throws IllegalArgumentException if {@code nThreads <= 0}
     */
    public static ExecutorService newFixedThreadPool(int nThreads) {
        return new ThreadPoolExecutor(nThreads, nThreads,
                                      0L, TimeUnit.MILLISECONDS,
                                      new LinkedBlockingQueue<Runnable>());
    }
```
<br><br>
(3)CachedThreadPool<br>
特点：大小可伸缩的线程池。如果当前没有可用线程，则创建一个线程。在执行结束后缓存60s，线程池的线程数可达到Integer.MAX_VALUE，即2147483647，内部使用SynchronousQueue作为阻塞队列，如果不被调用则移除线程。调用execute()方法时可以重用缓存中的线程。适用于很多短期异步任务的环境，可以提高程序性能。<br>
```
/**
     * Creates a thread pool that creates new threads as needed, but
     * will reuse previously constructed threads when they are
     * available.  These pools will typically improve the performance
     * of programs that execute many short-lived asynchronous tasks.
     * Calls to <tt>execute</tt> will reuse previously constructed
     * threads if available. If no existing thread is available, a new
     * thread will be created and added to the pool. Threads that have
     * not been used for sixty seconds are terminated and removed from
     * the cache. Thus, a pool that remains idle for long enough will
     * not consume any resources. Note that pools with similar
     * properties but different details (for example, timeout parameters)
     * may be created using {@link ThreadPoolExecutor} constructors.
     *
     * @return the newly created thread pool
     */
    public static ExecutorService newCachedThreadPool() {
        return new ThreadPoolExecutor(0, Integer.MAX_VALUE,
                                      60L, TimeUnit.SECONDS,
                                      new SynchronousQueue<Runnable>());
    }
```
<br><br>
(4)newScheduledThreadPool<br>
特点：初始化的线程池可以在指定的时间内周期性的执行所提交的任务，在实际的业务场景中可以使用该线程池定期的同步数据。<br>
```
/**
     * Creates a thread pool that can schedule commands to run after a
     * given delay, or to execute periodically.
     * @param corePoolSize the number of threads to keep in the pool,
     * even if they are idle.
     * @return a newly created scheduled thread pool
     * @throws IllegalArgumentException if {@code corePoolSize < 0}
     */
    public static ScheduledExecutorService newScheduledThreadPool(int corePoolSize) {
        return new ScheduledThreadPoolExecutor(corePoolSize);
    }
```
<br><br>
(5)newWorkStealingPool<br>
特点：newWorkStealingPool适合使用在很耗时的操作，但是newWorkStealingPool不是ThreadPoolExecutor的扩展，它是新的线程池类ForkJoinPool的扩展，但是都是在统一的一个Executors类中实现，由于能够合理的使用CPU进行对任务操作（并行操作），所以适合使用在很耗时的任务中；<br>
```
/**
     * Creates a thread pool that maintains enough threads to support
     * the given parallelism level, and may use multiple queues to
     * reduce contention. The parallelism level corresponds to the
     * maximum number of threads actively engaged in, or available to
     * engage in, task processing. The actual number of threads may
     * grow and shrink dynamically. A work-stealing pool makes no
     * guarantees about the order in which submitted tasks are
     * executed.
     *
     * @param parallelism the targeted parallelism level
     * @return the newly created thread pool
     * @throws IllegalArgumentException if {@code parallelism <= 0}
     * @since 1.8
     */
    public static ExecutorService newWorkStealingPool(int parallelism) {
        return new ForkJoinPool
            (parallelism,
             ForkJoinPool.defaultForkJoinWorkerThreadFactory,
             null, true);
    }
```
除了newWorkStealingPool的内部实现特殊一点之外，其它几个线程池都是基于ThreadPoolExecutor类实现的。<br><br>

## ThreadPoolExecutor的构造函数中的参数介绍
ThreadPoolExecutor的构造函数中有几个参数，现在介绍这些参数，是理解线程池工作原理的重要方式：<br><br>
1、第一个参数：int corePoolSIze，核心池大小，也就是线程池中会维持不被释放的线程数量。我们可以看到FixedThreadPool中这个参数值就是设定的线程数量，而SingleThreadExcutor中就是1，newCachedThreadPool中就是0，不会维持，只会缓存60L。但需要注意的是，在线程池刚创建时，里面并没有建好的线程，只有当有任务来的时候才会创建（除非调用方法prestartAllCoreThreads()与prestartCoreThread()方法），在corePoolSize数量范围的线程在完成任务后不会被回收。<br><br>
2、第二个参数：int maximumPoolSize，线程池的最大线程数，代表着线程池中能创建多少线程池。超出corePoolSize，小于maximumPoolSize的线程会在执行任务结束后被释放。此配置在CatchedThreadPool中有效。<br><br>
3、第三个参数：long keepAliveTime，刚刚说到的会被释放的线程缓存的时间。我们可以看到，正如我们所说的，在CachedThreadPool()构造过程中，会被设置缓存时间为60s（时间单位由第四个参数控制）。<br><br>
4、第四个参数：TimeUnit unit，设置第三个参数keepAliveTime的时间单位。<br><br>
5、第五个参数：存储等待执行任务的阻塞队列，有多种选择，分别介绍：<br><br>

SynchronousQueue——直接提交策略，适用于CachedThreadPool。它将任务直接提交给线程而不保持它们。如果不存在可用于立即运行任务的线程，则试图把任务加入队列将失败，因此会构造一个新的线程。此策略可以避免在处理可能具有内部依赖性的请求集时出现锁。直接提交通常要求最大的 maximumPoolSize 以避免拒绝新提交的任务（正如CachedThreadPool这个参数的值为Integer.MAX_VALUE）。当任务以超过队列所能处理的量、连续到达时，此策略允许线程具有增长的可能性。吞吐量较高。<br><br>

LinkedBlockingQueue——无界队列，适用于FixedThreadPool与SingleThreadExcutor。基于链表的阻塞队列，创建的线程数不会超过corePoolSizes（maximumPoolSize值与其一致），当线程正忙时，任务进入队列等待。按照FIFO原则对元素进行排序，吞吐量高于ArrayBlockingQueue。<br><br>

ArrayListBlockingQueue——有界队列，有助于防止资源耗尽，但是可能较难调整和控制。队列大小和最大池大小可能需要相互折衷：使用大型队列和小型池可以最大限度地降低 CPU 使用率、操作系统资源和上下文切换开销，但是可能导致人工降低吞吐量。如果任务频繁阻塞（例如，如果它们是 I/O边界），则系统可能为超过您许可的更多线程安排时间。使用小型队列通常要求较大的池大小，CPU使用率较高，但是可能遇到不可接受的调度开销，这样也会降低吞吐量。<br><br>

6、第六个参数：threadFactory，线程工厂，主要用来创建线程，可以通过自定义的线程工厂可以给每个新建的线程设置一个具有识别度的线程名。<br><br>

## 线程池的内部状态
<br>
```
private final AtomicInteger ctl = new AtomicInteger(ctlOf(RUNNING, 0));
    private static final int COUNT_BITS = Integer.SIZE - 3;
    private static final int CAPACITY   = (1 << COUNT_BITS) - 1;

    // runState is stored in the high-order bits
    private static final int RUNNING    = -1 << COUNT_BITS;
    private static final int SHUTDOWN   =  0 << COUNT_BITS;
    private static final int STOP       =  1 << COUNT_BITS;
    private static final int TIDYING    =  2 << COUNT_BITS;
    private static final int TERMINATED =  3 << COUNT_BITS;

    // Packing and unpacking ctl
    private static int runStateOf(int c)     { return c & ~CAPACITY; }
    private static int workerCountOf(int c)  { return c & CAPACITY; }
    private static int ctlOf(int rs, int wc) { return rs | wc; }
```
<br><br>
其中AtomicInteger变量ctl的功能非常强大：利用低29位表示线程池中线程数，通过高3位表示线程池的运行状态：<br><br>
1、RUNNING：-1 << COUNT_BITS，即高3位为111，该状态的线程池会接收新任务，并处理阻塞队列中的任务；<br>
2、SHUTDOWN： 0 << COUNT_BITS，即高3位为000，该状态的线程池不会接收新任务，但会处理阻塞队列中的任务；<br>
3、STOP ： 1 << COUNT_BITS，即高3位为001，该状态的线程不会接收新任务，也不会处理阻塞队列中的任务，而且会中断正在运行的任务；<br>
4、TIDYING ： 2 << COUNT_BITS，即高3位为010；<br>
5、TERMINATED： 3 << COUNT_BITS，即高3位为011；<br><br>
![](https://itmanmzt.github.io/styles/images/threadpool/001.jpg){:align="center"}<br><br>

## 线程池的初始化
默认情况下，创建线程池之后，线程池中是没有线程的，需要提交任务之后才会创建线程。
<br>
在实际中如果需要线程池创建之后立即创建线程，可以通过以下两个方法办到：
<br>
prestartCoreThread()：初始化一个核心线程；<br>
prestartAllCoreThreads()：初始化所有核心线程<br><br>
下面是这2个方法的实现：<br>
```
public boolean prestartCoreThread() {
    return addIfUnderCorePoolSize(null); //注意传进去的参数是null
}
 
public int prestartAllCoreThreads() {
    int n = 0;
    while (addIfUnderCorePoolSize(null))//注意传进去的参数是null
        ++n;
    return n;
}
```
<br><br>

## Handler任务拒绝策略
当用来缓存待处理任务的队列已满时，又加入了新的任务，那么这时候就该考虑如何处理这个任务。<br><br>
可以通过实现RejectedExceptionHandler接口，实现rejectedException(ThreadPoolExecutor e, Runnable r)方法自定义操作。但通常我们使用JDK提供了4种处理策略，在ThreadPoolExecutor构造时以参数传入：<br><br>
ThreadPoolExcutor.AbortPolicy()——直接抛出异常，默认操作<br>
ThreadPoolExcutor.CallerRunsPolicy()——只用调用者所在线程来运行任务<br>
ThreadPoolExcutor.DiscardOldersPolicy()——丢弃队列里最近的一个任务，并执行当前任务<br>
ThreadPoolExcutor.DiscardPolicy()——不处理，直接丢掉<br><br>

## execute和submit方法的区别
1、接收参数不一样，excute()方法需要一个Runnable类型参数，而submit方法需要一个Callable< T >类型参数。<br>
2、返回值不同，excute()方法没有返回值，submit方法会返回Future< T >类型返回值。<br>
3、submit方法适合处理异常。执行的任务里会抛出checked或者unchecked exception，而你又希望外面的调用者能够感知这些exception并做出及时的处理，那么就需要用到submit，通过捕获Future.get抛出的异常。<br>
4、submit方法内部调用了execute方法<br><br>

## 任务执行
```
/**
     * Executes the given task sometime in the future.  The task
     * may execute in a new thread or in an existing pooled thread.
     *
     * If the task cannot be submitted for execution, either because this
     * executor has been shutdown or because its capacity has been reached,
     * the task is handled by the current {@code RejectedExecutionHandler}.
     *
     * @param command the task to execute
     * @throws RejectedExecutionException at discretion of
     *         {@code RejectedExecutionHandler}, if the task
     *         cannot be accepted for execution
     * @throws NullPointerException if {@code command} is null
     */
    public void execute(Runnable command) {
        if (command == null)
            throw new NullPointerException();
        /*
         * Proceed in 3 steps:
         *
         * 1. If fewer than corePoolSize threads are running, try to
         * start a new thread with the given command as its first
         * task.  The call to addWorker atomically checks runState and
         * workerCount, and so prevents false alarms that would add
         * threads when it shouldn't, by returning false.
         *
         * 2. If a task can be successfully queued, then we still need
         * to double-check whether we should have added a thread
         * (because existing ones died since last checking) or that
         * the pool shut down since entry into this method. So we
         * recheck state and if necessary roll back the enqueuing if
         * stopped, or start a new thread if there are none.
         *
         * 3. If we cannot queue task, then we try to add a new
         * thread.  If it fails, we know we are shut down or saturated
         * and so reject the task.
         */
        int c = ctl.get();
        if (workerCountOf(c) < corePoolSize) {
            if (addWorker(command, true))
                return;
            c = ctl.get();
        }
        if (isRunning(c) && workQueue.offer(command)) {
            int recheck = ctl.get();
            if (! isRunning(recheck) && remove(command))
                reject(command);
            else if (workerCountOf(recheck) == 0)
                addWorker(null, false);
        }
        else if (!addWorker(command, false))
            reject(command);
    }
```
<br><br>
具体的执行流程如下：<br><br>
1、workerCountOf方法根据ctl的低29位，得到线程池的当前线程数，如果线程数小于corePoolSize，则执行addWorker方法创建新的线程执行任务；否则执行步骤2；<br><br>
2、如果线程池处于RUNNING状态，且把提交的任务成功放入阻塞队列中，则执行步骤3，否则执行步骤4；<br><br>
3、再次检查线程池的状态，如果线程池没有RUNNING，且成功从阻塞队列中删除任务，则执行reject方法(执行拒绝策略)处理任务；<br><br>
4、执行addWorker方法创建新的线程执行任务，如果addWoker执行失败，则执行reject方法处理任务；<br><br>
![](https://itmanmzt.github.io/styles/images/threadpool/003.jpg){:align="center"}<br><br>

从方法execute的实现可以看出：addWorker主要负责创建新的线程并执行任务，代码实现如下：<br><br>
```
private boolean addWorker(Runnable firstTask, boolean core) {
        retry:
        for (;;) {
            int c = ctl.get();
            int rs = runStateOf(c);

            // Check if queue empty only if necessary.
            if (rs >= SHUTDOWN &&
                ! (rs == SHUTDOWN &&
                   firstTask == null &&
                   ! workQueue.isEmpty()))
                return false;

            for (;;) {
                int wc = workerCountOf(c);
                if (wc >= CAPACITY ||
                    wc >= (core ? corePoolSize : maximumPoolSize))
                    return false;
                if (compareAndIncrementWorkerCount(c))
                    break retry;
                c = ctl.get();  // Re-read ctl
                if (runStateOf(c) != rs)
                    continue retry;
                // else CAS failed due to workerCount change; retry inner loop
            }
        }
```
<br><br>
这只是addWoker方法实现的前半部分：<br><br>
1、判断线程池的状态，如果线程池的状态值大于或等SHUTDOWN，则不处理提交的任务，直接返回；<br><br>
2、通过参数core判断当前需要创建的线程是否为核心线程，如果core为true，且当前线程数小于corePoolSize，则跳出循环，然后开始创建新的线程，具体实现如下：<br><br>
```
boolean workerStarted = false;
        boolean workerAdded = false;
        Worker w = null;
        try {
            final ReentrantLock mainLock = this.mainLock;
            w = new Worker(firstTask);
            final Thread t = w.thread;
            if (t != null) {
                mainLock.lock();
                try {
                    // Recheck while holding lock.
                    // Back out on ThreadFactory failure or if
                    // shut down before lock acquired.
                    int c = ctl.get();
                    int rs = runStateOf(c);

                    if (rs < SHUTDOWN ||
                        (rs == SHUTDOWN && firstTask == null)) {
                        if (t.isAlive()) // precheck that t is startable
                            throw new IllegalThreadStateException();
                        workers.add(w);
                        int s = workers.size();
                        if (s > largestPoolSize)
                            largestPoolSize = s;
                        workerAdded = true;
                    }
                } finally {
                    mainLock.unlock();
                }
                if (workerAdded) {
                    t.start();
                    workerStarted = true;
                }
            }
        } finally {
            if (! workerStarted)
                addWorkerFailed(w);
        }
        return workerStarted;
    }
```
<br><br>
线程池的工作线程通过Woker类实现，在ReentrantLock锁的保证下，把Woker实例插入到HashSet后，并启动Woker中的线程，其中Worker类设计如下：<br><br>
1、继承了AQS类，可以方便的实现工作线程的中止操作；<br><br>
2、实现了Runnable接口，可以将自身作为一个任务在工作线程中执行；<br><br>
3、当前提交的任务firstTask作为参数传入Worker的构造方法；<br><br>
```
 private final class Worker
        extends AbstractQueuedSynchronizer
        implements Runnable
    {
        /**
         * This class will never be serialized, but we provide a
         * serialVersionUID to suppress a javac warning.
         */
        private static final long serialVersionUID = 6138294804551838833L;

        /** Thread this worker is running in.  Null if factory fails. */
        final Thread thread;
        /** Initial task to run.  Possibly null. */
        Runnable firstTask;
        /** Per-thread task counter */
        volatile long completedTasks;

        /**
         * Creates with given first task and thread from ThreadFactory.
         * @param firstTask the first task (null if none)
         */
        Worker(Runnable firstTask) {
            setState(-1); // inhibit interrupts until runWorker
            this.firstTask = firstTask;
            this.thread = getThreadFactory().newThread(this);
        }

        /** Delegates main run loop to outer runWorker  */
        public void run() {
            runWorker(this);
        }

        // Lock methods
        //
        // The value 0 represents the unlocked state.
        // The value 1 represents the locked state.

        protected boolean isHeldExclusively() {
            return getState() != 0;
        }

        protected boolean tryAcquire(int unused) {
            if (compareAndSetState(0, 1)) {
                setExclusiveOwnerThread(Thread.currentThread());
                return true;
            }
            return false;
        }

        protected boolean tryRelease(int unused) {
            setExclusiveOwnerThread(null);
            setState(0);
            return true;
        }

        public void lock()        { acquire(1); }
        public boolean tryLock()  { return tryAcquire(1); }
        public void unlock()      { release(1); }
        public boolean isLocked() { return isHeldExclusively(); }

        void interruptIfStarted() {
            Thread t;
            if (getState() >= 0 && (t = thread) != null && !t.isInterrupted()) {
                try {
                    t.interrupt();
                } catch (SecurityException ignore) {
                }
            }
        }
    }
```
<br><br>
从Woker类的构造方法实现可以发现：线程工厂在创建线程thread时，将Woker实例本身this作为参数传入，当执行start方法启动线程thread时，本质是执行了Worker的runWorker方法。<br><br>
```
final void runWorker(Worker w) {
        Thread wt = Thread.currentThread();
        Runnable task = w.firstTask;
        w.firstTask = null;
        w.unlock(); // allow interrupts
        boolean completedAbruptly = true;
        try {
            while (task != null || (task = getTask()) != null) {
                w.lock();
                // If pool is stopping, ensure thread is interrupted;
                // if not, ensure thread is not interrupted.  This
                // requires a recheck in second case to deal with
                // shutdownNow race while clearing interrupt
                if ((runStateAtLeast(ctl.get(), STOP) ||
                     (Thread.interrupted() &&
                      runStateAtLeast(ctl.get(), STOP))) &&
                    !wt.isInterrupted())
                    wt.interrupt();
                try {
                    beforeExecute(wt, task);
                    Throwable thrown = null;
                    try {
                        task.run();
                    } catch (RuntimeException x) {
                        thrown = x; throw x;
                    } catch (Error x) {
                        thrown = x; throw x;
                    } catch (Throwable x) {
                        thrown = x; throw new Error(x);
                    } finally {
                        afterExecute(task, thrown);
                    }
                } finally {
                    task = null;
                    w.completedTasks++;
                    w.unlock();
                }
            }
            completedAbruptly = false;
        } finally {
            processWorkerExit(w, completedAbruptly);
        }
    }
```
<br><br>
runWorker方法是线程池的核心：<br><br>
1、线程启动之后，通过unlock方法释放锁，设置AQS的state为0，表示运行中断；<br><br>
2、获取第一个任务firstTask，执行任务的run方法，不过在执行任务之前，会进行加锁操作，任务执行完会释放锁；<br><br>
3、在执行任务的前后，可以根据业务场景自定义beforeExecute和afterExecute方法；<br><br>
4、firstTask执行完成之后，通过getTask方法从阻塞队列中获取等待的任务，如果队列中没有任务，getTask方法会被阻塞并挂起，不会占用cpu资源；<br><br>
```
 private Runnable getTask() {
        boolean timedOut = false; // Did the last poll() time out?

        retry:
        for (;;) {
            int c = ctl.get();
            int rs = runStateOf(c);

            // Check if queue empty only if necessary.
            if (rs >= SHUTDOWN && (rs >= STOP || workQueue.isEmpty())) {
                decrementWorkerCount();
                return null;
            }

            boolean timed;      // Are workers subject to culling?

            for (;;) {
                int wc = workerCountOf(c);
                timed = allowCoreThreadTimeOut || wc > corePoolSize;

                if (wc <= maximumPoolSize && ! (timedOut && timed))
                    break;
                if (compareAndDecrementWorkerCount(c))
                    return null;
                c = ctl.get();  // Re-read ctl
                if (runStateOf(c) != rs)
                    continue retry;
                // else CAS failed due to workerCount change; retry inner loop
            }

            try {
                Runnable r = timed ?
                    workQueue.poll(keepAliveTime, TimeUnit.NANOSECONDS) :
                    workQueue.take();
                if (r != null)
                    return r;
                timedOut = true;
            } catch (InterruptedException retry) {
                timedOut = false;
            }
        }
    }
```
<br><br>
整个getTask操作在自旋下完成：<br><br>
1、workQueue.take：如果阻塞队列为空，当前线程会被挂起等待；当队列中有任务加入时，线程被唤醒，take方法返回任务，并执行；<br><br>
2、workQueue.poll：如果在keepAliveTime时间内，阻塞队列还是没有任务，则返回null；<br><br>
所以，线程池中实现的线程可以一直执行由用户提交的任务。
<br><br>
## 线程池的关闭
ThreadPoolExecutor提供了两个方法，用于线程池的关闭，分别是shutdown()和shutdownNow()，其中：
<br>
shutdown()：不会立即终止线程池，而是要等所有任务缓存队列中的任务都执行完后才终止，但再也不会接受新的任务；<br>
shutdownNow()：立即终止线程池，并尝试打断正在执行的任务，并且清空任务缓存队列，返回尚未执行的任务；<br><br>

## 线程池容量的动态调整
ThreadPoolExecutor提供了动态调整线程池容量大小的方法：setCorePoolSize()和setMaximumPoolSize()；
<br>
setCorePoolSize：设置核心池大小；<br>

setMaximumPoolSize：设置线程池最大能创建的线程数目大小；<br><br>

## 使用线程池的好处
1、降低资源消耗。重复利用已创建线程，降低线程创建与销毁的资源消耗。<br>
2、提高响应效率。任务到达时，不需等待创建线程就能立即执行。<br>
3、提高线程可管理性。<br>
4、防止服务器过载。内存溢出、CPU耗尽。<br><br>

## 线程池应用范围
1、需要大量的线程来完成任务，并且完成所需时间较短。如Web服务器完成网页请求。<br>
2、对性能有苛刻要求。如服务器实时响应。<br>
3、突发性大量请求，且不至于在服务器产生大量线程。<br><br>

<br>

<br>

<center>有Marin的地方就有你的收获</center>