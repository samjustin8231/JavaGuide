
<!-- @import "[TOC]" {cmd="toc" depthFrom=1 depthTo=6 orderedList=false} -->

<!-- code_chunk_output -->

- [Runnable](#runnable)
- [Callable](#callable)
- [Feature](#feature)
- [FeatureTask](#featuretask)
- [CompletableFuture](#completablefuture)
- [参考文献](#参考文献)

<!-- /code_chunk_output -->


# Runnable

todo 

# Callable

todo 

# Feature

上面这段文字已经说明了Future的本质，一个Future代表一个异步计算的结果，并且它提供一些方法来让调用者检测异步过程是否完成，或者取得异步计算的结果，或者取消正在执行的异步任务。

Future接口在Java 5中被引入，设计初衷是对将来某个时刻会发生的结果进行建模。它建模 了一种异步计算，返回一个执行运算结果的引用，当运算结束后，这个引用被返回给调用方。在 Future中触发那些潜在耗时的操作把调用线程解放出来，让它能继续执行其他有价值的工作， 不再需要呆呆等待耗时的操作完成。

如上图所示，这种编程方式让你的线程可以在ExecutorService以并发方式调 用另一个线程执行耗时操作的同时，去执行一些其他的任务。接着，如果你已经运行到没有异步 操作的结果就无法继续任何有意义的工作时，可以调用它的get方法去获取操作的结果。如果操 作已经完成，该方法会立刻返回操作的结果，否则它会阻塞你的线程，直到操作完成，返回相应 的结果。如果该长时间运行的操作永远不返回了会怎样?为了处理这种可能性，虽然Future提供了一个无需任何参数的get方法，我们还是推荐大家使用重 载版本的get方法，它接受一个超时的参数，通过它，你可以定义你的线程等待Future结果的最 长时间，从而无需永无止境的等待下去。


Future接口

```
public interface Future<V> {
    boolean cancel(boolean mayInterruptIfRunning);

    boolean isCancelled();

    boolean isDone();

    V get() throws InterruptedException, ExecutionException;
   
    V get(long timeout, TimeUnit unit)
        throws InterruptedException, ExecutionException, TimeoutException;
}
```
# FeatureTask

todo

# CompletableFuture




# 参考文献

- [java并发编程--Runnable Callable及Future](https://www.cnblogs.com/MOBIN/p/6185387.html)
- [深度学习Java Future （一）](https://www.jianshu.com/p/bce9301f1adb)
- [Future接口和Callable接口以及FeatureTask详解](https://www.cnblogs.com/cuimiemie/p/6445154.html)
- [Java并发编程：Callable、Future和FutureTask](https://www.cnblogs.com/dolphin0520/p/3949310.html)
- [Java进阶之FutureTask的用法及解析](https://blog.csdn.net/chenliguan/article/details/54345993)


