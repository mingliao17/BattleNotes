#### 线程变量ThreadLocal

##### 概念

> ThreadLocal提供了线程的局部变量，每个线程都可以通过set()和get()来对这个局部变量进行操作，但不会和其他线程的局部变量进行冲突，实现了线程的数据隔离。

##### 方法

- get()：返回当前线程此线程局部变量的副本中的值
- initialValue()：返回此线程局部变量的当前线程的“初始值”
- remove()：
- set(T value)

##### 典型用途

- 数据库连接池——Connection
- Spring事务管理——TransactionSynchronizationManager

##### 容易发生什么问题

- 内存泄露，为啥呢？如何解决？
  - 为啥？ThreadLocal存值时，会把自己当做key存在ThreadLocalMap中，key被设计为WeakReference弱引用。这就导致ThreadLocal在没有外部强引用的时候，发生GC会被回收，如果创建ThreadLocal的线程一直持续运行，那这个Entry对象中的value就有可能一直得不到回收，就会发生内存泄露。
  - 如何解决？**使用完之后及时调用remove方法**

- 线程池中线程上下文丢失——网友发帖，待深究
- 并行流中线程上下文丢失——网友发帖，待深究

##### 示例

- 最简单，直击人心的示例

```java
public class SimpleThreadLocal {
    public static void main(String[] args) {
        ThreadLocal threadLocal = new ThreadLocal();
        threadLocal.set("mingliao");

        new Thread(()->{
            System.out.println("new thread print:"+threadLocal.get());
        }).start();

        System.out.println("main thread print:"+threadLocal.get());
    }
}

```

- 咋样才能给他搞一个内存泄露出来

```java
//首先加一下启动类JVM内存配置，设置固定最大内存为128MB：-Xmx128m

public class TryThreadLocalOOM {

    static final ThreadLocal<int[]> tlIntArray = new ThreadLocal<int[]>();

    private static final ThreadPoolExecutor threadPoolExecutor = new ThreadPoolExecutor
            (6,6,1, TimeUnit.SECONDS,new LinkedBlockingQueue<>());

    public static void main(String[] args) throws InterruptedException {
        for (int i = 0; i < 100 ; i++) {
            threadPoolExecutor.execute(new Runnable() {
                @Override
                public void run() {
                    tlIntArray.set(new int[1024*1024*5]);//建立比较大的对象，每个线程占用5m
                    tlIntArray.remove();//如果注释掉此行代码，就能看到oom效果，如果放开就正常跑
                    System.out.println(Thread.currentThread()+(System.currentTimeMillis()+""));
                }
            });
        }
    }
}
```







