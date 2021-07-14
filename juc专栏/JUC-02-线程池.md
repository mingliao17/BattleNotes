#### 线程池ThreadPoolExecutor

##### 作用

- 并发编程的艺术
  - 降低资源消耗
  - 提高响应速度
  - 提高线程的可管理性
- 码出高效java开发手册
  - 利用线程池管理并复用线、控制最大并发数等
  - 实现任务线程队列缓存策略和拒绝机制
  - 实现某些与时间相关的功能，如定时执行，周期执行等
  - 隔离线程环境。比如，交易服务和搜索服务在同一台服务器上，分别开启两个线程池，交易线程的资源消耗明显要大；因此，通过配置独立的线程池，将搅拌的交易服务与搜索服务隔离开，避免各服务线程相互影响

##### 状态

- `RUNNING`：能接受新任务，并处理阻塞队列中的任务
- `SHUTDOWN`：不接受新任务，但是可以处理阻塞队列中的任务
- `STOP`：不接受新任务，并且不处理阻塞队列中的任务，并且还打断正在运行任务的线程，就是直接撂担子不干了！
- `TIDYING`：所有任务都终止，并且工作线程也为0，处于关闭之前的状态
- `TERMINATED`：已关闭。

##### 参数

- `corePoolSize` - 常驻核心线程数，即使空闲时仍保留在池中的线程数，除非设置 allowCoreThreadTimeOut 
- `maximumPoolSize` - 池中允许的最大线程数 。必须大于或等于1，如果等于`corePoolSize`那就是固定大小线程池。队列缓存达到上限后，如果还有新任务需要处理，线程池就会创建新的线程。
- `keepAliveTime` - 表示线程池中线程的空闲时间，当空闲时间达到这个值，线程会被销毁，知道只剩下`corePoolSize`个线程为止，避免浪费内存和句柄资源
- `unit - keepAliveTime`-参数的时间单位 
- `workQueue` - 用于在执行任务之前使用的队列。 这个队列将仅保存 `execute` 方法提交的 `Runnable` 任务。当请求线程数大于核心线程数时，线程进入 BlockingQueue 阻塞队列。建议使用有界队列，可以增加系统的稳定性和预警能力
- `threadFactory` - 用于设置创建线程的工厂，可以通过线程工厂给每个创建出来的线程设置更有意义的名字
- `handler` - 饱和策略，执行被阻止时使用的处理程序，因为达到线程限制和队列容量，也是一种简单的限流保护
  - `AbortPolicy`：直接抛出异常（默认情况）
  - `CallerRunsPolicy`：只用调用者所在线程来运行任务
  - `DiscardOldestPolicy`：丢弃队列里最近的一个任务，并执行当前任务
  - `DiscardPolicy`：不处理，丢弃掉
  - 自定义策略：友好的拒绝策略有三种：保存到数据库进行削峰填谷，在空闲时再提取出来执行；转向某个提示页面；打印日志



##### 示例代码

线程工厂(顺便包含了任务的线程实现类)

```java
public class UserThreadFactory implements ThreadFactory {
    private final String namePrefix;
    private final AtomicInteger nextId = new AtomicInteger();

    //定义线程组名称，使用jstack排查问题时，有帮助
    public UserThreadFactory(String whatFeatureOfGroup) {
        this.namePrefix = "UserThreadFactory's "+whatFeatureOfGroup+"-Worker-";
    }

    @Override
    public Thread newThread(Runnable task) {
        String name = namePrefix + nextId.getAndIncrement();
        Thread thread = new Thread(null,task,name,0);
        System.out.println(thread.getName());
        return thread;
    }
}

class Task implements Runnable{
    private final AtomicLong count = new AtomicLong(0L);

    @Override
    public void run() {
        System.out.println("running_"+count.getAndIncrement());
    }
}
```



拒绝策略(看起来很敷衍的样子)

```java
public class UserRejectHandler implements RejectedExecutionHandler {
    @Override
    public void rejectedExecution(Runnable r, ThreadPoolExecutor executor) {
        System.out.println("task rejected. "+executor.toString());
    }
}
```



线程池主方法（池子1出发了拒绝策略，池子2却没有，因为队列2足够强大，看来哪里都是靠实力说话）

```java
public class UserThreadPool {

    public static void main(String[] args) {
        BlockingQueue queue1 = new LinkedBlockingQueue(2);
        BlockingQueue queue2 = new LinkedBlockingQueue(200);

        UserThreadFactory f1 = new UserThreadFactory("第一机房");
        UserThreadFactory f2 = new UserThreadFactory("第二机房");

        UserRejectHandler handler = new UserRejectHandler();

        //核心线程为1，最大线程为2，为了保证出发rejectHandler
        ThreadPoolExecutor threadPoolFirst = new ThreadPoolExecutor(
                1,2,60, TimeUnit.SECONDS,queue1,f1,handler);
        //利用第二个线程工厂实例创建第二个线程池，这里不会发生拒绝策略
        ThreadPoolExecutor threadPoolSecond = new ThreadPoolExecutor(
                1,2,60,TimeUnit.SECONDS,queue2,f2,handler);

        Runnable task = new Task();

        for (long i = 0; i < 200 ; i++) {
            threadPoolFirst.execute(task);
            threadPoolSecond.execute(task);
        }
    }
}
```

##### 使用时需注意

- 合理设置各类参数，应根据实际业务场景来设置合理的工作线程数
- 线程资源必须通过线程池提供，不允许应用中自行显式创建线程
- 创建线程或线程池时请指定有意义的线程名称，方便出错是回溯

##### 合理地配置线程池

根据任务特性来分角度分析

- 性质：
  - CPU密集型：尽可能小的配置线程，如“N+1”个线程的线程池
  - IO密集型：尽可能多的配置线程，如“2*N”个线程
  - 混合型任务：如果两个任务执行时间差别不大，可进行拆分为两个任务，如果差别太大就不用分解
- 优先级：高、中、低  （可考虑PriorityBlockingQueue优先级队列，可以让优先级高的任务先执行）
- 执行时间：长、中、短
- 依赖性：是否依赖其他系统资源，如数据库连接

```java
Runtime.getRuntime().availableProcessors();//获取当前cpu的核心数量
```

``