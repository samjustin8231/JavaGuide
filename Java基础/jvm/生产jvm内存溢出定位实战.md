# 生产jvm内存溢出定位实战

> 查看java进程

jps

jps -lvm 用于查看当前机器上运行的java进程。

jps用于列出Java的进程，jps可以增加参数，-m用于输出传递给Java进程的参数，-l用于输出主函数的完整路径，-v可以用于显示传递给jvm的参数。

> 查看某个进程的线程信息

```
top -H -p pid

如果提示：TERM environment variable not set.
则设置export TERM=dumb
```

> jstack

jstack -l pid

jstack -l 32318  查看此进程下线程的堆栈信息:


> jvisualvm



# 生产jvm参数配置

-Xss768k 设置每个线程的堆栈大小。JDK5.0以后每个线程堆栈大小为1M，以前每个线程堆栈大小为256K。更具应用的线程所需内存大小进行调整。在相同物理内存下，减小这个值能生成更多的线程。但是操作系统对一个进程内的线程数还是有限制的，不能无限生成，经验值在3000~5000左右。




# 参考文献

- [生产环境JVM内存溢出案例分析](https://blog.csdn.net/prestigeding/article/details/89075661)
- [JVM学习之jstat使用方法](https://www.cnblogs.com/parryyang/p/5772484.html)
- [JVM内存泄露———如何基于DUMP文件内存分析(众安)](https://wiki.zhonganonline.com/pages/viewpage.action?pageId=29205081)
- [内存溢出和内存泄漏](https://blog.csdn.net/qq_29699799/article/details/80173110)
- [一次线上内存泄漏的解决过程(帮助我解决了生产问题)](https://www.jianshu.com/p/f12244015c0d)