首先引用一下AbstractQueuedSynchronizer在官方文档中的内容，我感觉还是蛮清晰，但某些方面可能也是不讲人话

> 提供一个框架，用于实现依赖先进先出（FIFO——“first insert first out”）等待队列的阻塞锁和相关同步器（信号量，事件等）。该类被设计为大多数类型的同步器的有用依据，这些同步器依赖于单个原子`int`值来表示状态。子类必须定义改变此状态的受保护方法，以及根据该对象被获取或释放来定义该状态的含义。给定这些，这个类中的其他方法执行所有排队和阻塞机制。子类可以保持其他状态字段，但只以原子方式更新`int`使用方法操纵值getState()  ， setState(int)和compareAndSetState(int,  int)被跟踪相对于同步。
>
> 子类应定义为非公共内部助手类，用于实现其封闭类的同步属性。  `AbstractQueuedSynchronizer`类不实现任何同步接口。  相反，它定义了一些方法，如acquireInterruptibly(int)  ，可以通过具体的锁和相关同步器来调用适当履行其公共方法。 
>
> 此类支持默认*独占*模式和*共享*模式。  当以独占模式获取时，尝试通过其他线程获取不能成功。 多线程获取的共享模式可能（但不需要）成功。  除了在机械意义上，这个类不理解这些差异，当共享模式获取成功时，下一个等待线程（如果存在）也必须确定它是否也可以获取。  在不同模式下等待的线程共享相同的FIFO队列。 通常，实现子类只支持这些模式之一，但是两者都可以在`ReadWriteLock`中。  仅支持独占或仅共享模式的子类不需要定义支持未使用模式的方法。 
>
> 这个类定义的嵌套`AbstractQueuedSynchronizer.ConditionObject`可用于作为一类`Condition`由子类支持独占模式用于该方法的实施`isHeldExclusively()`份报告是否同步排他相对于保持在当前线程，方法`release(int)`与当前调用`getState()`值完全释放此目的，和`acquire(int)`  ，给定此保存的状态值，最终将此对象恢复到其先前获取的状态。  `AbstractQueuedSynchronizer`方法将创建此类条件，因此如果不能满足此约束，请勿使用该约束。  `AbstractQueuedSynchronizer.ConditionObject`的行为当然取决于其同步器实现的语义。 
>
> 该类为内部队列提供检查，检测和监控方法，以及条件对象的类似方法。  这些可以根据需要导出到类中，使用`AbstractQueuedSynchronizer`进行同步机制。 
>
> 此类的序列化仅存储底层原子整数维持状态，因此反序列化对象具有空线程队列。  需要可序列化的典型子类将定义一个`readObject`方法，可以将其恢复为`readObject`时的已知初始状态。
>
> - ### 用法 
>
>   使用这个类用作同步的基础上，重新定义以下方法，如适用，通过检查和/或修改使用所述同步状态`getState() ， `setState(int)`和/或`compareAndSetState(int,  int)` ： 
>
>   - `tryAcquire(int)` **独占式获取同步状态，试着获取，成功返回true，反之为false**
>   - `tryRelease(int)`**独占式释放同步状态，等待中的其他线程此时将有机会获取到同步状态；**
>   - `tryAcquireShared(int)`**共享式获取同步状态，返回值大于等于0，代表获取成功；反之获取失败；**
>   - `tryReleaseShared(int)`**共享式释放同步状态，成功为true，失败为false**
>   - `isHeldExclusively()`**是否在独占模式下被线程占用**

#### 基础方法

| 方法名称 | 作用 |
| -------- | ---- |
|void acquire(int arg) |以独占模式获取，忽略中断。  |
|void acquireInterruptibly(int arg) |以独占方式获得，如果中断，中止。  |
|void acquireShared(int arg) |以共享模式获取，忽略中断。  |
|void acquireSharedInterruptibly(int arg) |以共享方式获取，如果中断，中止。  |
|protected boolean compareAndSetState(int expect, int update) |如果当前状态值等于期望值，则将同步状态原子地设置为给定的更新值。  |
|Collection<Thread> getExclusiveQueuedThreads() |返回一个包含可能正在等待以独占模式获取的线程的集合。  |
|Thread getFirstQueuedThread() |返回队列中第一个（等待时间最长的）线程，或 null如果没有线程正在排队。  |
|Collection<Thread> getQueuedThreads() |返回一个包含可能正在等待获取的线程的集合。  |
|int getQueueLength() |返回等待获取的线程数的估计。  |
|Collection<Thread> getSharedQueuedThreads() |返回包含可能正在等待在共享模式下获取的线程的集合。  |
|protected int getState() |返回同步状态的当前值。  |
|Collection<Thread> getWaitingThreads(AbstractQueuedSynchronizer.ConditionObject condition) |返回一个集合，其中包含可能正在等待与此同步器相关联的给定条件的线程。  |
|int getWaitQueueLength(AbstractQueuedSynchronizer.ConditionObject condition) |返回等待与此同步器相关联的给定条件的线程数量的估计。  |
|boolean hasContended() |查询任何线程是否有争取获取此同步器; 那就是收购方法是否被阻止。  |
|boolean hasQueuedPredecessors() |查询任何线程是否等待获取比当前线程更长的时间。  |
|boolean hasQueuedThreads() |查询任何线程是否等待获取。  |
|boolean hasWaiters(AbstractQueuedSynchronizer.ConditionObject condition) |查询任何线程是否等待与此同步器相关联的给定条件。  |
|protected boolean isHeldExclusively() |返回 true如果同步仅针对当前（调用）线程进行保存。  |
|boolean isQueued(Thread thread) |如果给定的线程当前排队，则返回true。  |
|boolean owns(AbstractQueuedSynchronizer.ConditionObject condition) |查询给定的ConditionObject是否将此同步器用作其锁。  |boolean release(int arg) |以专属模式发布。  |
|boolean releaseShared(int arg) |以共享模式发布。  |
|protected void setState(int newState) |设置同步状态的值。  |
|String toString() |返回一个标识此同步器的字符串及其状态。  |
|protected boolean tryAcquire(int arg) |尝试以独占模式获取。  |
|boolean tryAcquireNanos(int arg, long nanosTimeout) |尝试以独占模式获取，如果中断则中止，如果给定的超时时间失败。  |
|protected int tryAcquireShared(int arg) |尝试以共享模式获取。  |
|boolean tryAcquireSharedNanos(int arg, long nanosTimeout) |尝试以共享模式获取，如果中断则中止，如果给定的时间超过，则失败。  |
|protected boolean tryRelease(int arg) |尝试设置状态以独占模式反映版本。  |
|protected boolean tryReleaseShared(int arg) |尝试将状态设置为以共享模式反映发布。  |

