### 概述

```text
java 定义了 Executor 接口并在该接口中定义了 execute() 用于执行一个线程任务，
  然后通过 ExecutorService 实现 Executor 接口并执行具体的线程操作。
  
ExecutorService 接口有多个实现类可用于创建不同的线程池：
 1. newCachedThreadPool：可缓存的线程池
 2. newFixedThreadPool：固定大小的线程池
 3. newScheduledThreadPool：可任务调度的线程池
 4. newSingleThreadExecutor：单个线程的线程池
 5. newWorksStealingPool：可快速运算的线程池，JDJK 1.8 新增
```

### newCachedThreadPool

```text
newCachedThreadPool 用于创建一个缓存线程池。
它在创建新线程时如果有可重用的线程，则重用他们，否则重新创建一个新的线程并将其添加到线程池中。

对于执行时间很短的任务而言，newCachedThreadPool 线程池能很大程度地重用线程进而提高系统的性能。
在线程池的 keepAliveTime 时间超过默认的 60 秒后，该线程会被终止并从缓存中移除，
因此在没有线程任务运行时，newCachedThreadPool 将不会占用系统的线程资源

在创建线程时需要执行申请CPU和内存、记录线程状态、控制阻塞等多项工作，复杂且耗时。
因此，在有执行时间很短的大量任务需要执行的情况下，
newCachedThreadPool能够很好地复用运行中的线程（任务已经完成但未关闭的线程）资源来提高系统
的运行效率。具体的创建方式如下：

**ExecutorService cachedThreadPool = Executors.newCachedThreadPool();**
```

### newFixedThreadPool

```text
newFixedThreadPool 用于创建一个固定想爱你成数量的线程池，
并将线程资源存放在队列中循环使用。

在 newFixedThreadPool 线程池中，若处于活动状态的线程数量
大于等于核心线程池的数量，则新提交的任务将在阻塞队列中排队，直到有可用的线程资源，
具体的创建方式如下：

**ExecutorService fixedThreadPool = Executors.newFixedThreadPool(5);**

```

### newScheduledThreadPool

```text
newScheduledThreadPool 创建一个可定时调度的线程池，
可设置在给定的延迟时间后执行或者定期执行某个线程任务：

**ScheduledExecutorService  scheduledThreadPool=**
**                    Executors.newScheduledThreadPool(3);**
**//1：创建一个延迟3秒执行的线程**
**scheduledThreadPool.schedule(newRunnable(){**
**        @Override**
**        public  void  run()  {**
**              System.out.println("delay  3  seconds  execu.");**
**}},  3,  TimeUnit.SECONDS);**
**//2：创建一个延迟1秒执行且每3秒执行一次的线程**
**scheduledThreadPool.scheduleAtFixedRate(newRunnable(){**
**      @Override**
**      public  void  run()  {**
**      System.out.println("delay  1  seconds, repeat  execute  every  3  seconds");**
**  }},1,3, TimeUnit.SECONDS);**


```

### newSingleThreadExecutor

```text
newSingleThreadExecutor 线程池会保证永远有且只有一个可用的线程，
在该线程停止或发生异常时，newSingleThreadExecutor 线程池会启动一
个新的线程来代替改线程继续执行任务：

**ExecutorService singleThread = Executors.newSingleThreadExecutor();**

```

### newWorksStealingPool

```text
newWorksStealingPool 创建持有足够线程的线程池来达到快速运算的目的，
在内部通过使用多个队列来减少各个线程调度产生的竞争。

这里所说的有足够的线程指JDK根据当前线程的运行需求向操作系统申请足够的线程，
以保障线程的快速执行，并很大程度地使用系统资源，提高并发计算的效率，
省去 用户根据 CPU 资源估算并行度的过程。

当然，如果开发者想自己定义线程的并发数，则也可以将其作为参数传入。
```

