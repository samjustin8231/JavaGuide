# ReentrantLock的内部实现
看完了AQS中的底层同步机制，我们来简单分析一下之前介绍过的ReentrantLock的实现原理。先回顾一下这个显式锁的典型使用方式：

```
Lock lock = new ReentrantLock();
lock.lock();
try {
    加锁后的代码
} finally {
    lock.unlock();     
}
```

复制代码ReentrantLock首先是一个显式锁，它实现了Lock接口。可能你已经忘记了Lock接口长啥样了，我们再回顾一遍：

```

public interface Lock {
    void lock();
    void lockInterruptibly() throws InterruptedException;
    boolean tryLock();
    boolean tryLock(long time, TimeUnit unit) throws InterruptedException;
    void unlock();
    Condition newCondition();
}
```

复制代码其实ReentrantLock内部定义了一个AQS的子类来辅助它实现锁的功能，由于ReentrantLock是工作在独占模式下的，所以它的lock方法其实是调用AQS对象的aquire方法去获取同步状态，unlock方法其实是调用AQS对象的release方法去释放同步状态，这些大家已经很熟了，就不再赘述了，我们大致看一下ReentrantLock的代码：

```

public class ReentrantLock implements Lock {

    private final Sync sync;    //AQS子类对象
    
    abstract static class Sync extends AbstractQueuedSynchronizer { 
        // ... 为节省篇幅，省略其他内容
    }
    
    // ... 为节省篇幅，省略其他内容
}
```

复制代码所以如果我们简简单单写下下边这行代码：

```
Lock lock = new ReentrantLock();
```

复制代码就意味着在内存里创建了一个ReentrantLock对象，一个AQS对象，在AQS对象里维护着同步队列的head节点和tail节点，不过初始状态下由于没有线程去竞争锁，所以同步队列是空的。
