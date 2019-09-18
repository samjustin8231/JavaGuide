
> JVM参数调优是架构师必备技能。

- 调优的目的：`减少垃圾回收次数`。
	- 因为垃圾回收的时候会是其他`的工作线程暂停。`
	- 如果JVM内存设置的比较小，则会比较频繁地回收。
- 垃圾回收的时候`尽量在新生代回收`，尽量少的在老年代回收。

# 一. JVM参数配置

JVM提供了诸多的参数进行JVM各个方面内存大小的设置，为Java应用进行优化提供了诸多的工具，本文将会详细分析各个参数的功能与使用。

```
-XX:+PrintGC      每次触发GC的时候打印相关日志
-XX:+UseSerialGC      串行回收
-XX:+PrintGCDetails  更详细的GC日志
-Xms               堆初始值
-Xmx               堆最大可用值
-Xmn               新生代堆最大可用值
-XX:SurvivorRatio  用来设置新生代中eden空间和from/to空间的比例.
-XX:NewRatio       配置新生代与老年代占比 1:2

含以-XX:SurvivorRatio=eden/from=den/to
总结:在实际工作中，我们可以直接将初始的堆大小与最大堆大小相等，
这样的好处是可以减少程序运行时垃圾回收次数，从而提高效率。
-XX:SurvivorRatio     用来设置新生代中eden空间和from/to空间的比例.

-Xss5m 设置最大调用深度
```

让垃圾回收最好在新生代回收，尽可能少的在老年代回收。

## 1.1 堆内存大小配置

JVM调优一般都是针对`堆内存`进行配置调优。

使用示例:  -Xms5m  -Xmx20m
说明： 当下Java应用最大可用内存为20M， 初始内存为5M

在run configuration中配置JVM arguments: -Xms5m  -Xmx20m

```
-Xms5m -Xmx20m

// byte[] b = new byte[4 * 1024 * 1024];
// System.out.println("分配了4M空间给数组");
System.out.print("最大内存");
System.out.println(Runtime.getRuntime().maxMemory() / 1024.0 / 1024 + "M");
System.out.print("可用内存");
System.out.println(Runtime.getRuntime().freeMemory() / 1024.0 / 1024 + "M");
System.out.print("已经使用内存");
System.out.println(Runtime.getRuntime().totalMemory() / 1024.0 / 1024 + "M");
```

## 1.2 配置新生代中edgn/survivor比例

使用示例:-Xms20m -Xmx20m -Xmn1m -XX:SurvivorRatio=2 -XX:+PrintGCDetails -XX:+UseSerialGC

说明：堆内存初始化值20m,堆内存最大值20m，新生代最大值可用1m，eden空间和from/to空间的比例为2/1

```
byte[] b = null;
for (int i = 0; i < 10; i++) {
	b = new byte[1 * 1024 * 1024];
}
```

GC日志分析

todo 

## 1.3 设置新生代与老年代比例参数

使用示例: -Xms20m -Xmx20m -XX:SurvivorRatio=2 -XX:+PrintGCDetails -XX:+UseSerialGC
-XX:NewRatio=2

说明：

- 堆内存初始化值20m,堆内存最大值20m，新生代最大值可用1m，eden
- 空间和from/to空间的比例为2/1
- 新生代和老年代的占比为1/2

# 二. OutOfMemoryError异常

## 2.1 Java堆溢出
错误原因: java.lang.OutOfMemoryError: Java heap space 堆内存溢出

解决办法:设置堆内存大小 // -Xms1m -Xmx10m -XX:+PrintGCDetails -XX:+HeapDumpOnOutOfMemoryError

```
// -Xms1m -Xmx10m -XX:+PrintGCDetails -XX:+HeapDumpOnOutOfMemoryError
List<Object> listObject = new ArrayList<>();
for (int i = 0; i < 10; i++) {
	System.out.println("i:" + i);
	Byte[] bytes = new Byte[1 * 1024 * 1024];
	listObject.add(bytes);
}
System.out.println("添加成功...");
```

## 2.2 虚拟机栈溢出
错误原因: java.lang.StackOverflowError  栈内存溢出
栈溢出 产生于递归调用，循环遍历是不会的，但是循环方法里面产生递归调用， 也会发生栈溢出。 

解决办法:设置线程最大调用深度
-Xss5m 设置最大调用深度

```
public class JvmDemo04 {
	 private static int count;
	 public static void count(){
		try {
			 count++;
			 count(); 
		} catch (Throwable e) {
			System.out.println("最大深度:"+count);
			e.printStackTrace();
		}
	 }
	 public static void main(String[] args) {
		 count();
	}
}

```

# 三. 内存溢出和内存泄漏

> 内存溢出与内存泄漏区别

Java内存泄漏就是没有及时清理内存垃圾，导致系统无法再给你提供内存资源（内存资源耗尽）；

在Java中，内存泄漏就是存在一些被分配的对象，这些对象有下面两个特点：

* 这些对象是可达的，即在有向图中，存在通路可以与其相连；
* 这些对象是无用的，即程序以后不会再使用这些对象。
如果对象满足这两个条件，这些对象就可以判定为Java中的内存泄漏，这些对象不会被GC所回收，然而它却占用内存。


而Java内存溢出就是你要求分配的内存超出了系统能给你的，系统不能满足需求，于是产生溢出。

内存溢出，这个好理解，说明存储空间不够大。就像倒水倒多了，从杯子上面溢出了来了一样。

内存泄漏，原因是，使用过的内存空间没有被及时释放，长时间占用内存，最终导致内存空间不足，而出现内存溢出。

内存泄漏包含了内存溢出。

# 四. Tomcat调优配置测试





# 工具


## jps
java process status 查看进程


jps用于列出Java的进程

参数含义如下：

- -q: 不输出 类名称、Jar 名称 和传入 main 方法的 参数；
- -l: 输出 main 类或 Jar 的 全限定名称；
- -m: 输出传入 main 方法的 参数；
- -v: 输出传入 JVM 的参数。

## jstat
jstat是一个可以用于观察Java应用程序运行时信息的工具，它的功能非常强大，可以通过它查看堆信息的详细情况。包括：类装载、内存、垃圾收集、jit编译信息等。

它的基本使用方法为：

```
jstat -<option> [-t] [-h<lines>] <vmid> [<interval> [<count>]]

选项option可以由以下值组成：

jstat -class pid:显示加载class的数量，及所占空间等信息。 
jstat -compiler pid:显示VM实时编译的数量等信息。 
jstat -gc pid:可以显示gc的信息，查看gc的次数，及时间。其中最后五项，分别是young gc的次数，young gc的时间，full gc的次数，full gc的时间，gc的总时间。 
jstat -gccapacity:可以显示，VM内存中三代（young,old,perm）对象的使用和占用大小，如：PGCMN显示的是最小perm的内存使用量，PGCMX显示的是perm的内存最大使用量，PGC是当前新生成的perm内存占用量，PC是但前perm内存占用量。其他的可以根据这个类推， OC是old内纯的占用量。 
jstat -gcnew pid:new对象的信息。 
jstat -gcnewcapacity pid:new对象的信息及其占用量。 
jstat -gcold pid:old对象的信息。 
jstat -gcoldcapacity pid:old对象的信息及其占用量。 
jstat -gcpermcapacity pid: perm对象的信息及其占用量。 
jstat -gcutil pid:统计gc信息统计。 
jstat -printcompilation pid:当前VM执行的信息。 
除了以上一个参数外，还可以同时加上 两个数字，如：jstat -printcompilation 3024 250 6是每250毫秒打印一次，一共打印6次。
```
这些参数中最常用的参数是gcutil，下面是该参数的输出介绍以及一个简单例子：　　

```
S0  — Heap上的 Survivor space 0 区已使用空间的百分比 
S1  — Heap上的 Survivor space 1 区已使用空间的百分比 
E   — Heap上的 Eden space 区已使用空间的百分比 
O   — Heap上的 Old space 区已使用空间的百分比 
P   — Perm space 区已使用空间的百分比 
YGC — 从应用程序启动到采样时发生 Young GC 的次数 
YGCT– 从应用程序启动到采样时 Young GC 所用的时间(单位秒) 
FGC — 从应用程序启动到采样时发生 Full GC 的次数 
FGCT– 从应用程序启动到采样时 Full GC 所用的时间(单位秒) 
GCT — 从应用程序启动到采样时用于垃圾回收的总时间(单位秒) 

[root@customercenter-wx-nuit-b2e8e6a6-676d58f5b7-d6b7v startup]# jstat -gcutil 1
  S0     S1     E      O      M     CCS    YGC     YGCT    FGC    FGCT     GCT   
  4.94   0.00  67.26  20.53  96.26  92.81     74    4.775     0    0.000    4.775
```


## jinfo
jinfo可以用来查看正在运行的Java应用程序的扩展参数，甚至在运行时修改部分参数，它的基本语法为：

```
jinfo  <option>  <pid>

1. jinfo可以查看运行时参数：
[root@customercenter-wx-nuit-b2e8e6a6-676d58f5b7-d6b7v startup]# jinfo -flag MaxTenuringThreshold 1    
-XX:MaxTenuringThreshold=6

2. jinfo还可以在运行时修改参数值：
> jinfo -flag PrintGCDetails 31518
-XX:-PrintGCDetails
> jinfo -flag +PrintGCDetails 31518
> jinfo -flag PrintGCDetails 31518
-XX:+PrintGCDetails


```

jinfo -flag -PrintGC
表示禁用GC日志的打印,注意是减号

jinfo -flag +PrintGC
表示启用GC日志的打印，注意是加号

jinfo -flag PrintGC
查看是否开启了GC日志的打印，注意没有加减号

## jmap
   
jmap命令主要用于生成堆快照文件，它的使用方法如下：

```
[root@customercenter-wx-nuit-b2e8e6a6-676d58f5b7-d6b7v logs]# jmap -dump:format=b,file=wx_nuit_dump_0917.hprof 1
Dumping heap to /alidata1/admin/wx-nuit/logs/wx_nuit_dump_0917.hprof ...
Heap dump file created
```
　
获得堆快照文件之后，我们可以使用多种工具对文件进行分析，例如jhat，visual vm等。


- jmap -finalizerinfo

	打印正等候回收的对象的信息。

- jmap -heap

	打印heap的概要信息，GC使用的算法，heap的配置及wise heap的使用情况

- jmap -clstats

	打印classload的信息。包含每个classloader的名字、活泼性、地址、父classloader和加载的class数量

- jmap   -histo[:live] 

	打印堆的直方图
- jmap -clstats

	打印classload的信息。包含每个classloader的名字、活泼性、地址、父classloader和加载的class数量

- jmap -dump:format=b,file=d:\a.bin pid

	生成堆信息，并存到d:\a.bin这个文件

- jmap -histo pid | more
	
	查看类和实例数量和详情：


## jhat（一般不用，内存占用较高）

jvm heap analysis tool jvm堆分析工具

分析堆信息文件，然后默认开7000端口，以网页的形式显示


使用jhat工具可以分析Java应用程序的堆快照文件，使用命令如下：

```
> jhat heap.hprof
Reading from heap.hprof...
Dump file created Tue Nov 11 06:02:05 CST 2014
Snapshot read, resolving...
Resolving 8781 objects...
Chasing references, expect 1 dots.
Eliminating duplicate references.
Snapshot resolved.
Started HTTP server on port 7000
Server is ready.
```
　　jhat在分析完成之后，使用HTTP服务器展示其分析结果，在浏览器中访问http://127.0.0.1:7000/即可得到分析结果。



## jstack
jstack可用于导出Java应用程序的线程堆栈信息，语法为：

```
jstack -l <pid>
```

使用jps命令查看进程号为32627，然后使用jstack -l 32637 > a.txt命令把堆栈信息打印到文件中，该文件内容如下。

在发生死锁时可以用jstack -l pid来观察锁持有情况

```
"Finalizer" #3 daemon prio=8 os_prio=1 tid=0x00000000033b9000 nid=0x268c in Object.wait() [0x000000001b7be000]
   java.lang.Thread.State: WAITING (on object monitor)
        at java.lang.Object.wait(Native Method)
        - waiting on <0x0000000780808ec8> (a java.lang.ref.ReferenceQueue$Lock)
        at java.lang.ref.ReferenceQueue.remove(ReferenceQueue.java:143)
        - locked <0x0000000780808ec8> (a java.lang.ref.ReferenceQueue$Lock)
        at java.lang.ref.ReferenceQueue.remove(ReferenceQueue.java:164)
        at java.lang.ref.Finalizer$FinalizerThread.run(Finalizer.java:209)

   Locked ownable synchronizers:
        - None

```

jstack -m
不仅会输出Java堆栈信息，还会输出C/C++堆栈信息（比如Native方法）


## jconsole

内存监控


## VisualVM 
VisualVM是JDK自带的一款全能型性能监控和故障分析工具,包括对CPU使用、JVM堆内存消耗、线程、类加载的实时监控,内存dump文件分析,垃圾回收运行情况的可视化分析等,对故障排查和性能调优很有帮助。在windows中安装JDK后,VisualVM位于%JAVA_HOME%/bin/下,直接执行jvisualvm.exe即可。

```
2014-11-11 21:33:12
Full thread dump Java HotSpot(TM) 64-Bit Server VM (24.55-b03 mixed mode):
 
"Attach Listener" daemon prio=5 tid=0x00007f8d0c803000 nid=0x3307 waiting on condition [0x0000000000000000]
   java.lang.Thread.State: RUNNABLE
 
   Locked ownable synchronizers:
    - None
 
"DestroyJavaVM" prio=5 tid=0x00007f8d0b80b000 nid=0x1903 waiting on condition [0x0000000000000000]
   java.lang.Thread.State: RUNNABLE
 
   Locked ownable synchronizers:
    - None
 
"north" prio=5 tid=0x00007f8d0c075000 nid=0x5103 waiting on condition [0x0000000115b06000]
   java.lang.Thread.State: WAITING (parking)
    at sun.misc.Unsafe.park(Native Method)
    - parking to wait for  <0x00000007d55ab600> (a java.util.concurrent.locks.ReentrantLock$NonfairSync)
    at java.util.concurrent.locks.LockSupport.park(LockSupport.java:186)
    at java.util.concurrent.locks.AbstractQueuedSynchronizer.parkAndCheckInterrupt(AbstractQueuedSynchronizer.java:834)
    at java.util.concurrent.locks.AbstractQueuedSynchronizer.doAcquireInterruptibly(AbstractQueuedSynchronizer.java:894)
    at java.util.concurrent.locks.AbstractQueuedSynchronizer.acquireInterruptibly(AbstractQueuedSynchronizer.java:1221)
    at java.util.concurrent.locks.ReentrantLock.lockInterruptibly(ReentrantLock.java:340)
    at DeadLock.run(DeadLock.java:48)
 
   Locked ownable synchronizers:
    - <0x00000007d55ab5d0> (a java.util.concurrent.locks.ReentrantLock$NonfairSync)
 
"south" prio=5 tid=0x00007f8d0c074800 nid=0x4f03 waiting on condition [0x0000000115a03000]
   java.lang.Thread.State: WAITING (parking)
    at sun.misc.Unsafe.park(Native Method)
    - parking to wait for  <0x00000007d55ab5d0> (a java.util.concurrent.locks.ReentrantLock$NonfairSync)
    at java.util.concurrent.locks.LockSupport.park(LockSupport.java:186)
    at java.util.concurrent.locks.AbstractQueuedSynchronizer.parkAndCheckInterrupt(AbstractQueuedSynchronizer.java:834)
    at java.util.concurrent.locks.AbstractQueuedSynchronizer.doAcquireInterruptibly(AbstractQueuedSynchronizer.java:894)
    at java.util.concurrent.locks.AbstractQueuedSynchronizer.acquireInterruptibly(AbstractQueuedSynchronizer.java:1221)
    at java.util.concurrent.locks.ReentrantLock.lockInterruptibly(ReentrantLock.java:340)
    at DeadLock.run(DeadLock.java:28)
 
   Locked ownable synchronizers:
    - <0x00000007d55ab600> (a java.util.concurrent.locks.ReentrantLock$NonfairSync)
 
"Service Thread" daemon prio=5 tid=0x00007f8d0c025800 nid=0x4b03 runnable [0x0000000000000000]
   java.lang.Thread.State: RUNNABLE
 
   Locked ownable synchronizers:
    - None
 
"C2 CompilerThread1" daemon prio=5 tid=0x00007f8d0c025000 nid=0x4903 waiting on condition [0x0000000000000000]
   java.lang.Thread.State: RUNNABLE
 
   Locked ownable synchronizers:
    - None
 
"C2 CompilerThread0" daemon prio=5 tid=0x00007f8d0d01b000 nid=0x4703 waiting on condition [0x0000000000000000]
   java.lang.Thread.State: RUNNABLE
 
   Locked ownable synchronizers:
    - None
 
"Signal Dispatcher" daemon prio=5 tid=0x00007f8d0c022000 nid=0x4503 runnable [0x0000000000000000]
   java.lang.Thread.State: RUNNABLE
 
   Locked ownable synchronizers:
    - None
 
"Finalizer" daemon prio=5 tid=0x00007f8d0d004000 nid=0x3103 in Object.wait() [0x000000011526a000]
   java.lang.Thread.State: WAITING (on object monitor)
    at java.lang.Object.wait(Native Method)
    - waiting on <0x00000007d5505568> (a java.lang.ref.ReferenceQueue$Lock)
    at java.lang.ref.ReferenceQueue.remove(ReferenceQueue.java:135)
    - locked <0x00000007d5505568> (a java.lang.ref.ReferenceQueue$Lock)
    at java.lang.ref.ReferenceQueue.remove(ReferenceQueue.java:151)
    at java.lang.ref.Finalizer$FinalizerThread.run(Finalizer.java:189)
 
   Locked ownable synchronizers:
    - None
 
"Reference Handler" daemon prio=5 tid=0x00007f8d0d001800 nid=0x2f03 in Object.wait() [0x0000000115167000]
   java.lang.Thread.State: WAITING (on object monitor)
    at java.lang.Object.wait(Native Method)
    - waiting on <0x00000007d55050f0> (a java.lang.ref.Reference$Lock)
    at java.lang.Object.wait(Object.java:503)
    at java.lang.ref.Reference$ReferenceHandler.run(Reference.java:133)
    - locked <0x00000007d55050f0> (a java.lang.ref.Reference$Lock)
 
   Locked ownable synchronizers:
    - None
```
　从这个输出可以知道：

 1、在输出的最后一段，有明确的"Found one Java-level deadlock"输出，所以通过jstack命令我们可以检测死锁；

 2、输出中包含了所有线程，除了我们的north,sorth线程外，还有"Attach Listener", "C2 CompilerThread0", "C2 CompilerThread1"等等；

 3、每个线程下面都会输出当前状态，以及这个线程当前持有锁以及等待锁，当持有与等待造成循环等待时，将导致死锁。


## jstatd命令

jstatd命令是一个RMI服务器程序，它的作用相当于代理服务器，建立本地计算机与远程监控工具的通信，jstatd服务器能够将本机的Java应用程序信息传递到远程计算机，由于需要多台计算机做演示，此处略。
# 垃圾收集器

todo 

# 参考文献
- [Java性能监控工具:VisualVM](https://www.cnblogs.com/winner-0715/p/7173792.html)
- [Java性能调优工具](https://www.cnblogs.com/timlearn/p/4088626.html)
- [JVM分析工具概述](https://juejin.im/post/5b59975fe51d4519700f70aa#heading-28)
- [JVM系列(七) - JVM线上监控工具](https://juejin.im/post/5b7044fe6fb9a009c249047e)

