# 一 、String、StringBuffer、StringBuilder简介

> 1、String（字符串常量）

a.Stirng是对象不是基本数据类型 
    
b.String是final类，不能被继承。是不可变对象，一旦创建，就不能修改它的值。 (线程安全的)


> 2、StringBuffer（字符串变量）

a.一个类似于 String 的字符串缓冲区，对它的修改的不会像String那样重创建对象。 

b.使用append()方法修改Stringbuffer的值，使用toString()方法转换为字符串。

c.线程安全的，建议多线程使用(通过synchronized实现)

注意:不能通过赋值符号对他进行赋值.

> 3、StringBuilder（字符串变量）

a.StringBuild是jdk1.5后用来替换stringBuffer的一个类，大多数时候可以替换StringBuffer。和StringBuffer的区别在于Stringbuild是一个单线程使用的类，不值执行线程同步所以比StringBuffer的速度快，效率高。

b.线程非安全的，建议单线程使用

注意:不能通过赋值符号对他进行赋值.

# 二、比较

1. String和StringBuffer（StringBuilder）的区别

	String 类型和 StringBuffer 类型的主要性能区别其实在于 String 是不可变的对象, 因此在每次对 String 类型进行改变的时候其实都等同于生成了一个新的 String 对象，然后将指针指向新的 String 对象，所以经常进行字符串连接操作的字符串最好不要用 String ，因为每次生成对象都会对系统性能产生影响，而且当内存中无引用对象多了以后， JVM 的 GC 就会开始工作，影响运行速度。
而如果是使用 StringBuffer 类则结果就不一样了，每次结果都会对 StringBuffer 对象本身进行操作，而不是生成新的对象。所以在字符串对象经常改变的情况下我们推荐使用 StringBuffer 。

2. StringBuffer和StringBuilder比较

	StringBuilder：线程非安全的
StringBuffer：线程安全的
他们的原理和操作基本相同，区别在于StringBuffer支持并发操作，线性安全的，适合多线程中使用。StringBuilder不支持并发操作，线性不安全的，不适合多线程中使用。新引入的StringBuilder类不是线程安全的，但其在单线程中的性能比StringBuffer高。

3. String和StringBuild、StringBuffer比较

	三者在字符串连接操作的执行速度方面的比较：StringBuilder > StringBuffer > String
	
	
# StringBuilder为什么是非线程安全的

todo 

```
public AbstractStringBuilder append(String str) {
    if (str == null)
        return appendNull();
    int len = str.length();
    ensureCapacityInternal(count + len);
    str.getChars(0, len, value, count);
    count += len;
    return this;
}

```
非原子操作


	
# 四、使用总结

1. 对于字符串连接操作较少的建议使用String
2. 对于字符串连接操作较频繁，并且是多线程操作，使用StringBuffer
3. 对于字符串连接操作比较频繁，但是是单线程操作的，建议使用StringBuilder。

