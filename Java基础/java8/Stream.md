

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


# Stream用法

## stream创建

创建流的方法有三种，分别是：Stream.of()、Stream.iterate()、Stream.generate()，然后，分别看一下这三个方法的声明。

```
static <T> Stream<T> of(T... values)

static <T> Stream<T> iterate(T seed, UnaryOperator<T> f)

static <T> Stream<T> generate(Supplier<T> s)
```

* Stream.of()：参数很简单，就是一系列的泛型参数。
* Stream.iterate()：第一个参数是一个初始值，第二个参数是一个操作。
* Stream.generate()：参数就是一个Supplier的供给型的参数。

流的创建有三种方法，分别是Stream.of()、Stream.iterate()、Stream.generate()，这几个都是 Stream 类的静态方法，所以，使用起来非常的方便。


> 举例

```
	/*
     * @Author 欧阳思海
     * @Description  创建流
     * @Date 11:05 2019/8/26
     * @Param []
     * @return void
     **/
    @Test
    public void testCreateStream() {
        //利用Stream.of方法创建流
        Stream<String> stream = Stream.of("hello", "world", "Java8");
        stream.forEach(System.out::println);
        System.out.println("##################");
        
        //利用Stream.iterate方法创建流
        Stream.iterate(10, n -> n + 1)
                .limit(5)
                .collect(Collectors.toList())
                .forEach(System.out::println);
        System.out.println("##################");
        
        //利用Stream.generate方法创建流
        Stream.generate(Math::random)
                .limit(5)
                .forEach(System.out::println);
        System.out.println("##################");
        
        //从现有的集合中创建流
        List<String> strings = Arrays.asList("hello", "world", "Java8");
        String string = strings.stream().collect(Collectors.joining(","));
        System.out.println(string);
    }
```




## 中间操作
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


## 终端操作

查找和匹配


* anyMath(Predicate)boolean：检查谓词是否至少匹配一个元素

* allMatch(Predicate)boolean：检查谓词是否匹配所有的元素

* noneMatch(boolean)Predicate：确保流中没有任何元素与给定的谓词匹配

* findAny()Optional：将返回当前流中的任意元素

* findFirst()Optional：将返回第一个元素

* forEach(Consumer):遍历每一个元素

Collect:对流进行处理

## 流的操作
### 装箱流

> 引入问题

在处理对象流的时候，可以利用 Collectors 类的静态方法转换为集合，例如，将字符串流转换为 List ，这种方式是没有问题的。

但是，如果遇到 double流想要转换为 List 时，这是就会报错。

```
DoubleStream.of(1.0, 2.0, 3.0)
                .collect(Collectors.toList());//错误的写法
```

这种方式就是错误的，编译是不能通过的。

别慌，对于这种问题，有 3 种比较好的解决方法。

- 1. 利用 boxed 方法

利用 boxed 方法，可以将 DoubleStream 转换为 Stream ，例如；

```
DoubleStream.of(1.0, 2.0, 3.0)
                .boxed()
                .collect(Collectors.toList());
```

- 2. 利用 mapToObj 方法

利用 mapToObj 方法也可以实现上面的功能，另外，也提供了 mapToInt、mapToLong、mapToDouble 等方法将基本类型流转换为相关包装类型。

```
DoubleStream.of(1.0, 2.0, 3.0)
                .mapToObj(Double::valueOf)
                .collect(Collectors.toList());
```

- 3. collect 方法

一般情况下，我们利用 collect 方法的时候，都是用于将流的数据收集为基本类型的集合，例如；

```
stream.collect(Collectors.toList())
```
然而，collect 方法其实还有一种更加一般化的形式，如下；


```
<R> R collect(Supplier<R> supplier,
                        ObjIntConsumer<R> accumulator,
                        BiCnsumer<R,R> combiner)
```
上面这种方法的第一个参数是一个供给器，相当于初始化一个容器，第二个参数是累加器，相当于给初始化的容器赋值，第三个参数是组合器，相当于将这些元素全部组合到一个容器。

下面，我们通过一个简单的例子来看看到底是怎么使用的！

```
List<Double> list = DoubleStream.of(1.0, 2.0, 3.0)
                .collect(ArrayList<Double>::new, ArrayList::add, ArrayList::addAll);
```
上面的例子我们可以看到，第一个参数：使用一个静态方法初始化一个 List 容器，第二个参数：使用静态方法 add ，添加元素，第三个参数：使用静态方法 addAll ，用于联合所有的元素。
从最后的返回值为 List，我们也可以看出，全部组合成一个初始化的 List 集合中了。

### 字符串与流之间的转换
这一小节主要讲解一下字符串与流之间的转换，将 String 转为流有两种方法，分别是 java.lang.CharSequence 接口定义的默认方法 chars 和 codePoints ，而将流转为字符串就是我们前面已经讲解到的方法 collect 。

```
/*
     * @Author 欧阳思海
     * @Description  字符串与流之间的转换
     * @Date 9:41 2019/9/2
     * @Param []
     * @return void
     **/
    @Test
    public void testString2Stream() {
        String s = "hello world Java8".codePoints()//转换成流
                .collect(StringBuffer::new,
                        StringBuffer::appendCodePoint,
                        StringBuffer::append)//将流转换为字符串
                .toString();

        String s1 = "hello world Java8".chars()//转换成流
                .collect(StringBuffer::new,
                        StringBuffer::appendCodePoint,
                        StringBuffer::append)//将流转换为字符串
                .toString();
    }

```
在上面的例子中，先用chars 和 codePoints 方法转换为流，然后都是利用 collect 方法再转回字符串。


###  流的映射 map 与 flatMap
流的映射是什么意思呢，我们先将一个在 Java8 之前的例子，我们常常需要将一个集合的对象的某一个字段取出来，然后再存到另外一个集合中，这种场景我们在 Java8 之前我们会这样实现。

但是，Java8 却改变了这种现实，我们来看一看怎么使用 map 和 flatMap。

首先，我们先看一下这俩个方法的声明；

```
<R> Stream<R> map(Function<? super T,? extends R> mapper)

<R> Stream<R> flatMap(Function<? super T,? extends Stream<? extends R>> mapper)
```
通过使用 map 方法，参数给定 Person::getName 映射出 name，然后再用 collect 收集到 List 中，就完成了上面的负责的操作，是不是很舒服。

但是，如果我们用 map 方法想要映射出 friends 属性，会遇到一个问题；

```
//映射出朋友
        List<List<Friend>> collect = list.stream().map(Person::getFriends).collect(Collectors.toList());
```
我们发现，上面的返回值是 List>，这种形式集合里面还包着集合，处理有点麻烦，但是，不是还有另外 flatMap 没有使用吗，这个方法正好能够解决这个问题。


```
List<Friend> collect1 = list.stream().flatMap(friend -> friend.getFriends().stream()).collect(Collectors.toList());
```
发现，这个方法的返回值是 List，正如我们看到的，flatMap 的方法能够“展平”包裹的流，这就是 map 和 flatMap 的区别。


### 流的连接
流的连接有两种方式，如果是两个流的连接，使用 Stream.concat 方法，如果是三个及三个以上的流的连接，就使用 Stream.flatMap 方法。

```
/**
     * @return void
     * @Author 欧阳思海
     * @Description 流的连接
     * @Date 10:13 2019/9/2
     * @Param []
     **/
    @Test
    public void testConcatStream() {
        //两个流的连接
        Stream<String> first = Stream.of("sihai", "sihai2", "sihai3");
        Stream<String> second = Stream.of("sihai4", "sihai5", "sihai6");
        Stream<String> third = Stream.of("siha7", "sihai8", "sihai9");
        Stream<String> concat = Stream.concat(first, second);

        //多个流的连接
        Stream<String> stringStream = Stream.of(first, second, third).flatMap(Function.identity());

    }
```

## 流的规约操作

todo 

## 流的查找与匹配

todo 

# 参考文献
* [Java8 Collectors.toMap的坑](https://blog.csdn.net/u013805360/article/details/82686009)
* [Java8 的 Stream 流式操作之王者归来](https://juejin.im/post/5d6d3b935188251325774f53#heading-4)