

CyclicBarrier适用于这样的情况：你希望创建一组任务，它们并行地执行工作，然后在下一个步骤之前等待，直到所有任务都完成。栅栏和闭锁的关键区别在于，所有线程必须同时到达栅栏位置，才能继续执行。

闭锁用于等待事件，而栅栏是线程之间彼此等待，等到都到的时候再决定做下一件事。可以参考Java并发工具类（闭锁CountDownLatch）

拿运动员的事情举例，运动员们跑到终点，互相等待所有人都到达终点后，再一起去做喝酒这件事。（运动员也许不能喝酒的，也许大家再跑一轮。）


# 1. CyclicBarrier 是什么？
从字面上的意思可以知道，这个类的中文意思是“循环栅栏”。大概的意思就是一个可循环利用的屏障。

它的作用就是会让所有线程都等待完成后才会继续下一步行动。

举个例子，就像生活中我们会约朋友们到某个餐厅一起吃饭，有些朋友可能会早到，有些朋友可能会晚到，但是这个餐厅规定必须等到所有人到齐之后才会让我们进去。这里的朋友们就是各个线程，餐厅就是 CyclicBarrier。

# 2. 怎么使用 CyclicBarrier
## 2.1 构造方法

```
public CyclicBarrier(int parties)
public CyclicBarrier(int parties, Runnable barrierAction)
```

解析：

* parties 是参与线程的个数
* 第二个构造方法有一个 Runnable 参数，这个参数的意思是最后一个到达线程要做的任务

## 2.2 重要方法

```
public int await() throws InterruptedException, BrokenBarrierException
public int await(long timeout, TimeUnit unit) throws InterruptedException, BrokenBarrierException, TimeoutException
```

> 解析：

* 线程调用 await() 表示自己已经到达栅栏
* BrokenBarrierException 表示栅栏已经被破坏，破坏的原因可能是其中一个线程 await() 时被中断或者超时

## 2.3 基本使用
### 2.3.1 需求
一个线程组的线程需要等待所有线程完成任务后再继续执行下一次任务

### 2.3.2 代码实现

```
public class CyclicBarrierDemo {

    static class TaskThread extends Thread {
        
        CyclicBarrier barrier;
        
        public TaskThread(CyclicBarrier barrier) {
            this.barrier = barrier;
        }
        
        @Override
        public void run() {
            try {
                Thread.sleep(1000);
                System.out.println(getName() + " 到达栅栏 A");
                barrier.await();
                System.out.println(getName() + " 冲破栅栏 A");
                
                Thread.sleep(2000);
                System.out.println(getName() + " 到达栅栏 B");
                barrier.await();
                System.out.println(getName() + " 冲破栅栏 B");
            } catch (Exception e) {
                e.printStackTrace();
            }
        }
    }
    
    public static void main(String[] args) {
        int threadNum = 5;
        CyclicBarrier barrier = new CyclicBarrier(threadNum, new Runnable() {
            
            @Override
            public void run() {
                System.out.println(Thread.currentThread().getName() + " 完成最后任务");
            }
        });
        
        for(int i = 0; i < threadNum; i++) {
            new TaskThread(barrier).start();
        }
    }
    
}
```
打印结果：

```
Thread-1 到达栅栏 A
Thread-3 到达栅栏 A
Thread-0 到达栅栏 A
Thread-4 到达栅栏 A
Thread-2 到达栅栏 A
Thread-2 完成最后任务
Thread-2 冲破栅栏 A
Thread-1 冲破栅栏 A
Thread-3 冲破栅栏 A
Thread-4 冲破栅栏 A
Thread-0 冲破栅栏 A
Thread-4 到达栅栏 B
Thread-0 到达栅栏 B
Thread-3 到达栅栏 B
Thread-2 到达栅栏 B
Thread-1 到达栅栏 B
Thread-1 完成最后任务
Thread-1 冲破栅栏 B
Thread-0 冲破栅栏 B
Thread-4 冲破栅栏 B
Thread-2 冲破栅栏 B
Thread-3 冲破栅栏 B
```
从打印结果可以看出，所有线程会等待全部线程到达栅栏之后才会继续执行，并且最后到达的线程会完成 Runnable 的任务。

# 3. CyclicBarrier 使用场景
可以用于多线程计算数据，最后合并计算结果的场景。

# 4. CyclicBarrier 与 CountDownLatch 区别
* CountDownLatch 是一次性的，CyclicBarrier 是可循环利用的
* CountDownLatch 参与的线程的职责是不一样的，有的在倒计时，有的在等待倒计时结束。CyclicBarrier 参与的线程职责是一样的。

# 参考文献

- [Java并发工具类（栅栏CyclicBarrier）](https://juejin.im/post/5a3267a66fb9a045132ab988)
- [CyclicBarrier 使用详解](https://www.jianshu.com/p/333fd8faa56e)