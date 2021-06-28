#### 线程池框架Executor

##### 参数

- corePool：核心线程池大小

- maximumPool：最大线程池大小
- BlockingQueue：暂时保存任务的工作队列
- RejectedExecutionHandler：饱和策略

##### 类型

- FixedThreadPool：定长线程池
  - 指定工作线程数量的线程池，提交一个任务新建一个线程，直到饱和；
- SingleThreadExecutor：单例线程池
  - 循环使用，只会有一个线程
- CachedThreadPool：会根据需要创建新线程的线程池 
- scheduledThreadPoolExecutor：周期性线程池

#### FutureTask



#### ScheduledThreadPoolExecutor

给定延迟之后运行任务，或者定期执行任务