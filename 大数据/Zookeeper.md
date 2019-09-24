# Zookeeper


> 节点类型

Zookeeper中ZNode的节点创建时候是可以指定类型的，主要有下面几种类型。

- PERSISTENT：持久化ZNode节点，一旦创建这个ZNode点存储的数据不会主动消失，除非是客户端主动的delete。
- EPHEMERAL：临时ZNode节点，Client连接到Zookeeper Service的时候会建立一个Session，之后用这个Zookeeper连接实例创建该类型的znode，一旦Client关闭了Zookeeper的连接，服务器就会清除Session，然后这个Session建立的ZNode节点都会从命名空间消失。总结就是，这个类型的znode的生命周期是和Client建立的连接一样的。
- PERSISTENT_SEQUENTIAL：顺序自动编号的ZNode节点，这种znoe节点会根据当前已近存在的ZNode节点编号自动加 1，而且不会随Session断开而消失。
- EPEMERAL_SEQUENTIAL：临时自动编号节点，ZNode节点编号会自动增加，但是会随Session消失而消失


# 参考文献

* [咱们一起聊聊Zookeeper](https://juejin.im/post/5b03d58a6fb9a07a9e4d8f01)