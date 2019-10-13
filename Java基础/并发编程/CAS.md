在Java并发中，我们最初接触的应该就是synchronized关键字了，但是synchronized属于重量级锁，很多时候会引起性能问题，volatile也是个不错的选择，但是volatile不能保证原子性，只能在某些场合下使用。

像synchronized这种独占锁属于悲观锁，它是在假设一定会发生冲突的，那么加锁恰好有用，除此之外，还有乐观锁，乐观锁的含义就是假设没有发生冲突，那么我正好可以进行某项操作，如果要是发生冲突呢，那我就重试直到成功，乐观锁最常见的就是CAS。

我们在读Concurrent包下的类的源码时，发现无论是ReenterLock内部的AQS，还是各种Atomic开头的原子类，内部都应用到了CAS，最常见的就是我们在并发编程时遇到的i++这种情况。传统的方法肯定是在方法上加上synchronized关键字:

```

public class Test {

    public volatile int i;

    public synchronized void add() {
        i++;
    }
}
```

但是这种方法在性能上可能会差一点，我们还可以使用AtomicInteger，就可以保证i原子的++了。


```
public class Test {

    public AtomicInteger i;

    public void add() {
        i.getAndIncrement();
    }
}
```

我们来看getAndIncrement的内部：

```

public final int getAndIncrement() {
    return unsafe.getAndAddInt(this, valueOffset, 1);
}
```
再深入到getAndAddInt():

```
public final int getAndAddInt(Object var1, long var2, int var4) {
    int var5;
    do {
        var5 = this.getIntVolatile(var1, var2);
    } while(!this.compareAndSwapInt(var1, var2, var5, var5 + var4));

    return var5;
}
```

这里我们见到compareAndSwapInt这个函数，它也是CAS缩写的由来。那么仔细分析下这个函数做了什么呢？

首先我们发现compareAndSwapInt前面的this，那么它属于哪个类呢，我们看上一步getAndAddInt，前面是unsafe。这里我们进入的Unsafe类。这里要对Unsafe类做个说明。结合AtomicInteger的定义来说：


```
public class AtomicInteger extends Number implements java.io.Serializable {
    private static final long serialVersionUID = 6214790243416807050L;
    
    // setup to use Unsafe.compareAndSwapInt for updates
    private static final Unsafe unsafe = Unsafe.getUnsafe();
    private static final long valueOffset;
    
    static {
        try {
            valueOffset = unsafe.objectFieldOffset
                (AtomicInteger.class.getDeclaredField("value"));
        } catch (Exception ex) { throw new Error(ex); }
    }
    
    private volatile int value;
    ...
```
在AtomicInteger数据定义的部分，我们可以看到，其实实际存储的值是放在value中的，除此之外我们还获取了unsafe实例，并且定义了valueOffset。再看到static块，懂类加载过程的都知道，static块的加载发生于类加载的时候，是最先初始化的，这时候我们调用unsafe的objectFieldOffset从Atomic类文件中获取value的偏移量，那么valueOffset其实就是记录value的偏移量的。

再回到上面一个函数getAndAddInt，我们看var5获取的是什么，通过调用unsafe的getIntVolatile(var1, var2)，这是个native方法，具体实现到JDK源码里去看了，其实就是获取var1中，var2偏移量处的值。var1就是AtomicInteger，var2就是我们前面提到的valueOffset,这样我们就从内存里获取到现在valueOffset处的值了。

现在重点来了，compareAndSwapInt（var1, var2, var5, var5 + var4）其实换成compareAndSwapInt（obj, offset, expect, update）比较清楚，意思就是如果obj内的value和expect相等，就证明没有其他线程改变过这个变量，那么就更新它为update，如果这一步的CAS没有成功，那就采用自旋的方式继续进行CAS操作，取出乍一看这也是两个步骤了啊，其实在JNI里是借助于一个CPU指令完成的。所以还是原子操作。

# CAS底层原理

CAS全称呼Compare-And-Swap，它是一条CPU并发原语

他的功能是判断内存某个位置的值是否为预期值，如果是则更改为新的值，这个过程是原子的。


```
    public final int getAndIncrement() {
        return unsafe.getAndAddInt(this, valueOffset, 1);
    }

```
Unsafe类中的所有方法都是native修饰的，也就是说Unsafe类中的方法都直接调用操作系统底层资源执行相应任务

CAS并发原语体现在JAVA语言中就是sun.misc.Unsafe类中各个方法。调用Unsafe类中的CAS方法，JVM会帮我们实现CAS汇编指令。这是一种完全依赖于硬件的功能，通过他实现了原子操作。由于CAS是一种系统原语，原语属于操作系统用语范畴，是由若干条指令组成的，用于完成某个功能的一个过程，并且原语的执行必须是连续的，在执行过程中不允许被中断，也就是说CAS是一条CPU的原子指令，不会造成数据不一致问题。



# CAS 的问题

1. ABA问题

	CAS需要在操作值的时候检查下值有没有发生变化，如果没有发生变化则更新，但是如果一个值原来是A，变成了B，又变成了A，那么使用CAS进行检查时会发现它的值没有发生变化，但是实际上却变化了。这就是CAS的ABA问题。
	
	常见的解决思路是使用版本号。在变量前面追加上版本号，每次变量更新的时候把版本号加一，那么A-B-A 就会变成1A-2B-3A。
	
	目前在JDK的atomic包里提供了一个类AtomicStampedReference来解决ABA问题。这个类的compareAndSet方法作用是首先检查当前引用是否等于预期引用，并且当前标志是否等于预期标志，如果全部相等，则以原子方式将该引用和该标志的值设置为给定的更新值。

2. 循环时间长开销大

	使用CAS时非阻塞同步，也就是说不会将线程挂起，会自旋（无非就是一个死循环）进行下一次尝试，如果这里自旋时间过长对性能是很大的消耗。如果JVM能支持处理器提供的pause指令，那么在效率上会有一定的提升。

3. 只能保证一个共享变量的原子操作

    当对一个共享变量执行操作时CAS能保证其原子性，如果对多个共享变量进行操作,CAS就不能保证其原子性。有一个解决方案是利用对象整合多个共享变量，即一个类中的成员变量就是这几个共享变量。然后将这个对象做CAS操作就可以保证其原子性。atomic中提供了AtomicReference来保证引用对象之间的原子性。


# 参考文献

- [Java CAS 原理剖析](https://juejin.im/post/5a73cbbff265da4e807783f5)
- [彻底理解synchronized](https://juejin.im/post/5ae6dc04f265da0ba351d3ff)