> 是以驷牡异力，而六辔如琴；并驾齐驱，而一毂统福。行文如此，并发亦如此。

## JUC是个啥
各路大佬口中的术语juc即Java并发包java.util.concurrent下的内容

Java程序员进行并发编程时，相比于其他语言的程序员而言要倍感幸福，因为并发编程大师Doug Lea不遗余力地为Java开发者提供了非常多的并发容器和框架。顺手放一张这位老师的照片
![这大爷名叫Doug Lea](https://bkimg.cdn.bcebos.com/pic/2934349b033b5bb502db9e8436d3d539b600bcb8?x-bce-process=image/resize,m_lfit,w_268,limit_1/format,f_jpg)

>如果IT的历史，是以人为主体串接起来的话，那么肯定少不了Doug Lea。这个鼻梁挂着眼镜，留着德王威廉二世的胡子，脸上永远挂着谦逊腼腆笑容，服务于纽约州立大学Oswego分校计算机科学系的老大爷。说他是这个世界上对Java影响力最大的一个人，一点也不为过。因为两次Java历史上的大变革，他都间接或直接的扮演了举足轻重的角色。2004年所推出的Tiger。Tiger广纳了15项JSRs(Java Specification Requests)的语法及标准，其中一项便是JSR-166。JSR-166是来自于Doug编写的util.concurrent包。


## JUC都有啥

### 线程同步类/并发工具类
**在不同的线程之间，通过传递同步信号量来协调线程执行的先后次序**

 - CountDownLatch——计数器/闭锁器，基于执行时间的同步类，允许一个或多个线程等待其他线程完成操作
 - Semaphore——信号量，基于空闲信号的同步情况，当Semphore的permits定义为1时，就是互斥锁，当permits>1时就是共享锁
 - CyclicBarrier——循环屏障（也有人称之为栅栏）,基于同步达到某个点的信号量触发机制

### 并发容器和框架
  - ConcurrentHashMap——线程安全且高效的HashMap
  - ConcurrentLinkedQueue——非阻塞的线程安全队列
  - Concurrent※※※——※※※更多高级的并发容器
  - Fork/Join——并行执行任务框架

### 原子操作类（共12个）
- 原子更新基本类型类
    - AtomicBoolean——原子更新布尔类型
    - AtomicInteger——原子更新整型
    - AtomicLong——原子更新长整型
- 原子更新数组
    - AtomicIntegerArray——原子更新整型数组里的元素
    - AtomicLongArray——原子更新长整型数组中的元素
    - AtomicReferenceArray——原子更新引用类型数组里的元素
- 原子更新引用类型
    - AtomicReference——原子更新引用类型
    - AtomicReferenceFieldUpdater——原子更新引用类型里的字段
    - AtomicMarkableReference——原子更新带有标记位的引用类型
- 原子更新字段类
    - AtomicIntegerFieldUpdater——原子更新整型的字段的更新器
    - AtomicLongFieldUpdater——原子更新长整型字段的更新器
    - AtomicStampedReference——原子更新带有版本号的引用类型

### 线程管理类
  - ThreadPoolExecutor——线程池
  - Executors——线程池框架
  - ScheduleExecutorService——给定延迟之后运行任务，或者定期执行任务。可在构造函数指定多个对应的后台线程数
  - FutureTask——可取消的异步计算。该类提供了一个Future的基本实现 ，具有启动和取消计算的方法，查询计算是否完整，并检索计算结果。 结果只能在计算完成后才能检索; 如果计算尚未完成，则get方法将阻止。 一旦计算完成，则无法重新启动或取消计算（除非使用runAndReset()调用计算 ）。 

### 锁相关类
  - Lock——锁，用来控制多个线程访问共享资源的方式
  - ReentrantLock——重入锁，能够支持一个线程对资源的重复加锁
  - ReentrantReadWriteLock——读写锁，维护了一对锁，一个读锁和一个写锁。
  - Condition——监视器方法，配合lock实现等待/通知模式
  - AbstractQueuedSynchronizer——队列同步器，简称AQS，用来构建锁或其他同步组件的基础框架

### 其他
  - TimeUnit——时间单位工具类
  - ※※※Exception——并发异常类

## 参考书籍
- 《Java并发编程的艺术》
- 《码出高效Java开发手册》

日后将持续更新JUC子类相关博文，敬请期待！