# 定义
计数信号量用来控制同时访问某个特定资源的操作数量，或者同时执行某个指定操作的数量。信号量还可以用来实现某种资源池，或者对容器施加边界。

Semaphore管理着一组许可（permit）,许可的初始数量可以通过构造函数设定，操作时首先要获取到许可，才能进行操作，操作完成后需要释放许可。如果没有获取许可，则阻塞到有许可被释放。如果初始化了一个许可为1的Semaphore，那么就相当于一个不可重入的互斥锁（Mutex）。

> 实例场景

理论的听起来有些绕口，其实假设生活中一个常见的场景：每天早上，大家都热衷于带薪上厕所，但是公司厕所一共只有10个坑位。。那么只能同时10个人用着，后面来的人都得等着（阻塞），如果走了2个人，那么又可以进去2个人。这里面就是Semaphore的应用场景，争夺有限的资源。

> 代码实战

```
package concurrency;

import java.util.Random;
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;
import java.util.concurrent.Semaphore;
import java.util.concurrent.TimeUnit;


class Employee implements Runnable {
    private String id;
    private Semaphore semaphore;
    private static Random rand= new Random(47);

    public Employee(String id, Semaphore semaphore) {
        this.id = id;
        this.semaphore = semaphore;
    }

    public void run() {
            try {
                semaphore.acquire();
                System.out.println(this.id + "is using the toilet");
                TimeUnit.MILLISECONDS.sleep(rand.nextInt(2000));
                semaphore.release();
                System.out.println(this.id + "is leaving");
            } catch (InterruptedException e) {
            }
    }
}

public class ToiletRace {
    private static final int THREAD_COUNT = 30;

    private static ExecutorService threadPool = Executors
            .newFixedThreadPool(THREAD_COUNT);

    private static Semaphore s = new Semaphore(10);

    public static void main(String[] args) {
        for (int i = 0; i < THREAD_COUNT; i++) {
            threadPool.execute(new Employee(String.valueOf(i), s));
        }

        threadPool.shutdown();
    }
}


```

这里我定义了30个人要上厕所，但是只有10个坑位，每个人消耗随机的时间，直接运行上面这段代码，可以看到一开始进去了10个人，后来就是陆陆续续的有人进，有人出了。但是正在使用的一定不会超过10个的。

Semaphore是很好用的Java并发工具，除了上面这个例子，我们在工作中经常用它管理数据库连接或者保护其它受限资源的并发使用。当然Semaphore还有其它的一些方法，可以查看剩余的许可数，可以查看正在使用许可的线程数，具体使用时可以查看官方文档。




# 参考文献

- [Java并发工具类（信号量Semaphore）](https://juejin.im/post/5a38d2046fb9a045076fcb1f)
- [Semaphore：实现一个限流器](https://juejin.im/post/5d7ef114e51d4561ac7bcd64)