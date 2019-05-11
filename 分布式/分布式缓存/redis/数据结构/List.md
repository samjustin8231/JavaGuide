

todo 未完待续。。。

# List
列表（list）类型是用来存储多个 有序 的 字符串。在 Redis 中，可以对列表的 两端 进行 插入（push）和 弹出（pop）操作，还可以获取 指定范围 的 元素列表、获取 指定索引下标 的 元素 等。

列表 是一种比较 灵活 的 数据结构，它可以充当 栈 和 队列 的角色，在实际开发上有很多应用场景。

如图所示，a、b、c、d、e 五个元素 从左到右 组成了一个 有序的列表，列表中的每个字符串称为 元素（element），一个列表最多可以存储 2 ^ 32 - 1 个元素。

# 相关命令


## 1. 查

* 获取指定范围内的元素列表

```
lrange key start stop
```



lrange 操作会获取列表 指定索引 范围所有的元素。
索引下标 有两个特点：


1. 索引下标 从左到右 分别是 0 到 N-1，但是 从右到左 分别是 -1 到 -N。

2. lrange 中的 end 选项包含了 自身，这个和很多编程语言不包含 end 不太相同。


从左到右 获取列表的第 2 到第 4 个元素，可以执行如下操作：

```
127.0.0.1:6379> lrange listkey 1 3
1) "redis"
2) "b"
3) "a"
```

从右到左 获取列表的第 1 到第 3 个元素，可以执行如下操作：

```
127.0.0.1:6379> lrange listkey -3 -1
1) "redis"
2) "b"
3) "a"
```

* 获取列表指定索引下标的元素

```
lindex key index
```

例如当前列表 最后一个 元素为 a：
127.0.0.1:6379> lindex listkey -1
"a"
* 获取列表长度

```
llen key
```

例如，下面示例 当前列表长度 为 4：

```
127.0.0.1:6379> llen listkey
(integer) 4
```



## 2. 增
### 从右边插入元素


```
rpush key value [value ...]
```


下面代码 从右向左 插入元素 c、b、a：

```
127.0.0.1:6379> rpush listkey c b a
(integer) 3
```

### 从左边插入元素


```
lpush key value [value ...]
```


使用方法和 rpush 相同，只不过从 左侧插入，这里不再赘述。

### 向某个元素前或者后插入元素

```
linsert key before|after pivot value
```



linsert 命令会从 列表 中找到 第一个 等于 pivot 的元素，在其 前（before）或者 后（after）插入一个新的元素 value，例如下面操作会在列表的 元素 b 前插入 redis：

```
127.0.0.1:6379> linsert listkey before b redis
(integer) 4
```


复制代码返回结果为 4，代表当前 列表 的 长度，当前列表变为：

```
127.0.0.1:6379> lrange listkey 0 -1
1) "c"
2) "redis"
3) "b"
4) "a"
```



## 3. 删

## 4. 改



# 参考文献
[深入剖析Redis系列(七) - Redis数据结构之列表](https://juejin.im/post/5bcb3b066fb9a05d1c14c712)