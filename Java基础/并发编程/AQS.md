# 问题
（1）AQS是什么？

（2）AQS的定位？

（3）AQS的实现原理？

（4）基于AQS实现自己的锁？

# 简介
AQS的全称是AbstractQueuedSynchronizer，它的定位是为Java中几乎所有的锁和同步器提供一个基础框架。

AQS是基于FIFO的队列实现的，并且内部维护了一个状态变量state，通过原子更新这个状态变量state即可以实现加锁解锁操作。

本章及后续章节的内容理解起来可能会比较晦涩，建议先阅读彤哥上一章的内容【死磕 java同步系列之自己动手写一个锁Lock】。

# 

# 参考文献

- [Java技术之AQS详解](https://www.jianshu.com/p/da9d051dcc3d)
- [死磕 java同步系列之AQS起篇](https://mp.weixin.qq.com/s/nAqgec8GscULz6DkkYFINg)