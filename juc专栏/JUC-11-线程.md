#### 线程的几种状态 

- new——新建（线程被创建且未启动的状态）
  - 继承Thread类（调用start方法）
  - 实现Runnable接口（调用run方法）
  - 实现Callable接口（调用call方法）
- runnable——就绪（调用start()之后运行之前的状态）
- running——运行（run()正在执行时线程的状态，可能由于某些因素而退出running，如时间、异常、锁、调度等）
- blocked——阻塞（即阻塞状态）
  - 同步阻塞——锁被其他线程占用
  - 主动阻塞——调用Thread的某些方法，主动让出cpu执行权，比如sleep()、join()等
  - 等待阻塞——执行了wait()
- dead——终止（即终止状态，是run执行结束，或因异常退出后的状态，此状态不可逆装

#### 如何保证高并发场景下的线程安全

- 数据单线程内可见
- 只读对象
- 线程安全类
- 同步与锁机制