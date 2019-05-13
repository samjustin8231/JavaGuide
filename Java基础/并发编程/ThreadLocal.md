todo code demo

# 什么是ThreadLocal
　　ThreadLocal，很多地方叫做线程本地变量，也有些地方叫做线程本地存储，其实意思差不多。可能很多朋友都知道ThreadLocal为变量在每个线程中都创建了一个副本，那么每个线程可以访问自己内部的副本变量。




# 如何使用

# 原理

# ThreadLocal源码分析
ThreadLocal 是怎样保证其值在各个线程中是独立的呢？下面分析下 ThreadLocal 的实现。

ThreadLocal 是构造函数只是一个简单的无参构造函数，并且没有任何实现。

## ThreadLocal的内部实现
### 1.set(T value) 方法

### 2.get() 方法

### 3.initialValue() 方法

### 4.remove() 方法

### 5.当前线程的 ThreadLocalMap

## ThreadLocalMap的内部实现



# 内存泄漏的问题

# 参考文献
* [Java 之 ThreadLocal 详解](https://juejin.im/post/5965ef1ff265da6c40737292#heading-8)
* [Java并发编程：深入剖析ThreadLocal](https://www.cnblogs.com/dolphin0520/p/3920407.html)