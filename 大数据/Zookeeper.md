# 什么是Zookeeper

官方文档上这么解释zookeeper，它是一个分布式服务框架，是Apache Hadoop 的一个子项目，它主要是用来解决分布式应用中经常遇到的一些数据管理问题，如：统一命名服务、状态同步服务、集群管理、分布式应用配置项的管理等。

上面的解释有点抽象，简单来说zookeeper=文件系统+监听通知机制。

ZooKeeper并不直接暴露分布式服务所需要的原语及原语的调用方法。什么是原语？举个例子，比如说分布式锁机制是一个原语，它会暴露出创建、获取、释放三个调用方法。ZooKeeper以类似文件系统的方式存储数据，暴漏出调用这些数据的API。让应用通过ZooKeeper的机制和API，自己来实现分布式相关原语。


# 节点类型

Zookeeper中ZNode的节点创建时候是可以指定类型的，主要有下面几种类型。

- PERSISTENT：持久化ZNode节点，一旦创建这个ZNode点存储的数据不会主动消失，除非是客户端主动的delete。
- EPHEMERAL：临时ZNode节点，Client连接到Zookeeper Service的时候会建立一个Session，之后用这个Zookeeper连接实例创建该类型的znode，一旦Client关闭了Zookeeper的连接，服务器就会清除Session，然后这个Session建立的ZNode节点都会从命名空间消失。总结就是，这个类型的znode的生命周期是和Client建立的连接一样的。
- PERSISTENT_SEQUENTIAL：顺序自动编号的ZNode节点，这种znoe节点会根据当前已近存在的ZNode节点编号自动加 1，而且不会随Session断开而消失。
- EPEMERAL_SEQUENTIAL：临时自动编号节点，ZNode节点编号会自动增加，但是会随Session消失而消失

# 事件通知
todo 

# 应用场景

## 命名服务(注册中心)
dubbo的注册中心

consul, eureka, zookeeper, redis 

## 配置中心

## 分布式锁

## 消息中间件
事件通知，实现发布-订阅功能

## 分布式事务

全局协调者

## 可以实现选举策略

## 实现负载均衡

在本地实现负载均衡，dubbo服务的负载均衡功能


# 参考文献

* [咱们一起聊聊Zookeeper](https://juejin.im/post/5b03d58a6fb9a07a9e4d8f01)
* [Zookeeper典型应用场景介绍](https://blog.csdn.net/u013468915/article/details/80955110)
* [原创ZooKeeper入门实战教程（一）-介绍与核心概念](https://blog.csdn.net/liyiming2017/article/details/83035157)
* [Zookeeper学习系列【一】 教会你Zookeeper的一些基础概念](https://juejin.im/post/5cda1fc5f265da0385581983)