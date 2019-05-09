
函数式接口

* Function
* Predicate
* Consumer
* Supplier


方法引用


lambda表达式





# Stream

> Stream的理解

1. java.util.Stream的一个接口，简称流，可以处理数据更加方便。可以看成遍历数据集的高级迭代器。
2. 提供串行和并行两种模式进行汇聚操作，并发模式能够充分利用多核处理器的优势。
3. Stream 可以并行化操作，迭代器只能命令式地、串行化操作。
4. Stream 的另外一大特点是，数据源本身可以是无限的。


Java8中的Stream是对集合功能的一种增强，主要用于对集合对象进行各种非常便利高效的聚合和大批量数据的操作。结合Lambda表达式可以极大的提高开发效率和代码可读性。

> 以前的写法

```
for (Shape shape : shapes){
    shape.setColor(RED)
}
```
外部迭代问题

* for循环是串行的，而且必须按照集合中元素的顺序依次进行；
* 集合框架无法对控制流进行优化，例如通过排序、并行、短路求值以及惰性求值改善性能。


## Stream用法

### stream创建

```
Arrays.asList(1, 2);
```

### 中间操作
* filter(Predicate)
* map(Function) 接收一个函数作参数，该函数会将每一个元素传入的值映射成另外的一个元素，按照1:1的比例

* flatMap(Function):Stream：一对多的映射，层级结构扁平化，就是将最底层元素抽出来放到一起。

* distinct(),会去除相同的元素，根据元素的hashCode和equals方法实现。

```
 List<String> mylist=transactions.stream()
                .map(transaction -> transaction.getTrader().getCity())
                .distinct()
                .collect(Collectors.toList());
```

* anyMatch
* limit(int):Stream：返回一个不超过给定长度的流，用来获取前N个值。
* skip(int):Stream：返回一个跳掉前面N个值的流，跟limit()方法互补。
* sorted():Stream:自定义比较


### 终端操作

查找和匹配


* anyMath(Predicate)boolean：检查谓词是否至少匹配一个元素

* allMatch(Predicate)boolean：检查谓词是否匹配所有的元素

* noneMatch(boolean)Predicate：确保流中没有任何元素与给定的谓词匹配

* findAny()Optional：将返回当前流中的任意元素

* findFirst()Optional：将返回第一个元素

* forEach(Consumer):遍历每一个元素

Collect:对流进行处理



## 并行流


# Optional

Optional<T>类(java.util.Optional)是一个容器类，代表一个值存在或不存在。
常用方法：

* isPresent()：将在Optional包含值的时候返回true,否则返回flase。
* ifPresent(Consumer<T> block)：会在值存在的时候执行给定的代码块。
* T get()：会在值存在时返回值。
* T orElse(T other)会在值存在时返回值。


# Date



# 参考文献
[Java8 Stream的总结](https://juejin.im/post/5a8a71506fb9a0633d71e2c9)
[Java8:流 学会用流](https://www.jianshu.com/p/c733e1a2cb9b)

