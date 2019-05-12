
架构师必备技能。

# JVM参数配置

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

## 堆内存大小配置

JVM调优一般都是针对堆内存进行配置调优。

使用示例:  -Xmx20m -Xms5m  
说明： 当下Java应用最大可用内存为20M， 初始内存为5M

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

## 配置新生代比例

使用示例:-Xms20m -Xmx20m -Xmn1m -XX:SurvivorRatio=2 -XX:+PrintGCDetails -XX:+UseSerialGC

说明：堆内存初始化值20m,堆内存最大值20m，新生代最大值可用1m，eden空间和from/to空间的比例为2/1

```
byte[] b = null;
for (int i = 0; i < 10; i++) {
	b = new byte[1 * 1024 * 1024];
}
```

## 设置新生代与老年代比例参数

使用示例: -Xms20m -Xmx20m -XX:SurvivorRatio=2 -XX:+PrintGCDetails -XX:+UseSerialGC
-XX:NewRatio=2

说明：堆内存初始化值20m,堆内存最大值20m，新生代最大值可用1m，eden空间和from/to空间的比例为2/1
新生代和老年代的占比为1/2

# OutOfMemoryError异常

## Java堆溢出
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

## 虚拟机栈溢出
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

# 内存溢出和内存泄漏

内存溢出与内存泄漏区别

Java内存泄漏就是没有及时清理内存垃圾，导致系统无法再给你提供内存资源（内存资源耗尽）；

而Java内存溢出就是你要求分配的内存超出了系统能给你的，系统不能满足需求，于是产生溢出。

内存溢出，这个好理解，说明存储空间不够大。就像倒水倒多了，从杯子上面溢出了来了一样。

内存泄漏，原因是，使用过的内存空间没有被及时释放，长时间占用内存，最终导致内存空间不足，而出现内存溢出。

内存泄漏包含了内存溢出。

# Tomcat调优配置测试


JMeter工具


# 工具


jps

jmap



## jconsole


## 

# 参考文献
