




# Zookeeper Service网络结构
Zookeeper的工作集群可以简单分成两类，一个是Leader，唯一一个，其余的都是follower，如何确定Leader是通过内部选举确定的。

Leader和各个follower是互相通信的，对于Zookeeper系统的数据都是保存在内存里面的，同样也会备份一份在磁盘上。
如果Leader挂了，Zookeeper集群会重新选举，在毫秒级别就会重新选举出一个Leader。
集群中除非有一半以上的Zookeeper节点挂了，Zookeeper Service才不可用。

# 读写数据
## 写数据
一个客户端进行写数据请求时，如果是follower接收到写请求，就会把请求转发给Leader，Leader通过内部的Zab协议进行原子广播，直到所有Zookeeper节点都成功写了数据后（内存同步以及磁盘更新），这次写请求算是完成，然后Zookeeper Service就会给Client发回响应。
 
## 读数据
因为集群中所有的Zookeeper节点都呈现一个同样的命名空间视图（就是结构数据），上面的写请求已经保证了写一次数据必须保证集群所有的Zookeeper节点都是同步命名空间的，所以读的时候可以在任意一台Zookeeper节点上。


# 参考

- [咱们一起聊聊Zookeeper](https://juejin.im/post/5b03d58a6fb9a07a9e4d8f01#heading-7)