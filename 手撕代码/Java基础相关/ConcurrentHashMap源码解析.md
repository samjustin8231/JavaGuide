# ConcurrentHashMap源码解析

HashMap的相关源码实现，并且我们知道它是线程不安全的，在并发环境中使用时，HashMap在扩容的时候有可能会生成一个环形链表，从而导致get形成死循环超时。那这篇我们就来介绍一下并发环境下使用的HashMap——ConcurrentHashMap，下面是它的类关系图。


![](ConcurrentHashMap类图.png)


