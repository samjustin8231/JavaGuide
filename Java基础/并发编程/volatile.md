# 并发编程有三个重要特性

- 原子性
 
	所谓原子性是指在一次的操作或者多次的操作中，`要么所有的操作全部都得到了执行并且不会受到任何因素的干扰而中断，要么所有的操作都不执行`。i++不能保证原子性，volatile关键字不保证数据的原子性，synchronized关键字保证
多个原子性操作合在一起就不是原子性操作了
简单的读取和赋值操作是原子性的，将一个变量赋值给另外一个变量的操作不是原子性的
由于synchronized是一种排他机制，因此被他修饰的同步代码是无法被中途打断的，因此其能够保证代码的原子性。
- 可见性

	可见性是指当一个线程对共享变量进行了修改，那么另外的线程可以立即看到修改后的最新值
`synchronized和Lock保证可见性`，他们会在（monitor exit）锁释放之前，会将对变量（共享资源）的修改刷新到主内存中
volatile具有保证可见性的语义

- 有序性

	有序性是指程序代码在执行过程中的先后顺序，由于Java在编译器以及运行期的优化，导致了代码的执行顺序未必就是开发者编写代码时的顺序
单利模式中的double-check的写法就是利用了volatile的顺序性

# volatile实现原理

volatile保证了不同线程对共享变量操作时的可见性，也就是说当一个线程修改了volatile修饰的变量，另外一个线程会立即看到最新的值

volatile禁止JVM和处理器对使用volatile修饰的关键字进行指令进行重排序


todo


# volatile应用点

1. 单例模式DCL代码

DCL （Double Check Lock双端检锁机制）在加锁前和加锁后都进行一次判断

```
    public static SingletonDemo getInstance() {
        if (instance == null) {
            synchronized (SingletonDemo.class) {
                if (instance == null) {
                    instance = new SingletonDemo();
                }
            }
        }
        return instance;
    }

```

DCL（双端检锁）机制不一定线程安全，原因时有指令重排的存在，加入volatile可以禁止指令重排

原因是在某一个线程执行到第一次检测，读取到instance不为null时，instance的引用对象可能没有完成初始化。instance=new SingleDemo();可以被分为一下三步（伪代码）：

```
memory = allocate();//1.分配对象内存空间
instance(memory);	//2.初始化对象
instance = memory;	//3.设置instance执行刚分配的内存地址，此时instance!=null

```

步骤2和步骤3不存在数据依赖关系，而且无论重排前还是重排后程序的执行结果在单线程中并没有改变，因此这种重排优化时允许的，如果3步骤提前于步骤2，但是instance还没有初始化完成

但是指令重排只会保证串行语义的执行的一致性（单线程），但并不关心多线程间的语义一致性。

所以当一条线程访问instance不为null时，由于instance示例未必已初始化完成，也就造成了线程安全问题。

为解决以上问题，可以将SingletongDemo实例上加上volatile



# 参考文献

- [回顾《深入理解 Java 虚拟机》 之内存模型和 volatile 关键字](https://chenjiayang.me/2019/02/12/jvm-java-volatile/)