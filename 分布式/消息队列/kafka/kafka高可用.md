# 为何需要Replication
　　在Kafka在0.8以前的版本中，是没有Replication的，一旦某一个Broker宕机，则其上所有的Partition数据都不可被消费，这与Kafka数据持久性及Delivery Guarantee的设计目标相悖。同时Producer都不能再将数据存于这些Partition中。

　　如果Producer使用同步模式则Producer会在尝试重新发送message.send.max.retries（默认值为3）次后抛出Exception，用户可以选择停止发送后续数据也可选择继续选择发送。而前者会造成数据的阻塞，后者会造成本应发往该Broker的数据的丢失。

　　如果Producer使用异步模式，则Producer会尝试重新发送message.send.max.retries（默认值为3）次后记录该异常并继续发送后续数据，这会造成数据丢失并且用户只能通过日志发现该问题。同时，Kafka的Producer并未对异步模式提供callback接口。

　　由此可见，在没有Replication的情况下，一旦某机器宕机或者某个Broker停止工作则会造成整个系统的可用性降低。随着集群规模的增加，整个集群中出现该类异常的几率大大增加，因此对于生产系统而言Replication机制的引入非常重要。

# Leader Election
　　引入Replication之后，同一个Partition可能会有多个Replica，而这时需要在这些Replication之间选出一个Leader，Producer和Consumer只与这个Leader交互，其它Replica作为Follower从Leader中复制数据。

　　因为需要保证同一个Partition的多个Replica之间的数据一致性（其中一个宕机后其它Replica必须要能继续服务并且即不能造成数据重复也不能造成数据丢失）。如果没有一个Leader，所有Replica都可同时读/写数据，那就需要保证多个Replica之间互相（**N×N条通路**）同步数据，数据的一致性和有序性非常难保证，大大增加了Replication实现的复杂性，同时也增加了出现异常的几率。而引入Leader后，只有Leader负责数据读写，Follower只向Leader顺序Fetch数据（N条通路），系统更加简单且高效。

# 如何将所有Replica均匀分布到整个集群
为了更好的做负载均衡，Kafka尽量将所有的Partition均匀分配到整个集群上。一个典型的部署方式是一个Topic的Partition数量大于Broker的数量。同时为了提高Kafka的容错能力，也需要将同一个Partition的Replica尽量分散到不同的机器。实际上，如果所有的Replica都在同一个Broker上，那一旦该Broker宕机，该Partition的所有Replica都无法工作，也就达不到HA的效果。同时，如果某个Broker宕机了，需要保证它上面的负载可以被均匀的分配到其它幸存的所有Broker上。

Kafka分配Replica的算法如下：

1.将所有Broker（假设共n个Broker）和待分配的Partition排序

2.将第i个Partition分配到第（i mod n）个Broker上

3.将第i个Partition的第j个Replica分配到第（(i + j) mode n）个Broker上


# kafka的高可用

kafka不是完全同步，也不是完全异步，是一种ISR机制：  

1. leader会维护一个与其基本保持同步的Replica列表，该列表称为ISR(in-sync Replica)，每个Partition都会有一个ISR，而且是由leader动态维护 

2. 如果一个follower比一个leader落后太多，或者超过一定时间未发起数据复制请求，则leader将其重ISR中移除 

3. 当ISR中所有Replica都向Leader发送ACK时，leader才commit，这时候producer才能认为一个请求中的消息都commit了。

这里的核心问题是，在海量的 topic 情况下，或者经常性的流量抖动情况下，我们不能对 topic 的producer 每次打过来的消息数目做任何假设，所以就不太好定出来一个 合适的 eplica.lag.max.messages 值

一个配置全部搞定

其实只有两种情况是异常的，一种就是卡住，另外一种是follower 性能慢，如果我们只根据 follower 落后 leader 多少来判断是否应该把 follower 提出ISR集合，就必须要对流量进行预测估计，怎么才能避免这种不靠谱的估计呢，kafka 给出 的方案是这样的，对   replica.lag.time.max.ms 这个配置的含义做了增强，和之前一样，如果 follower 卡住超过这个时间不发送fetch请求， 会被踢出ISR集合，新的增强逻辑是，在 follower 落后 leader 超过   eplica.lag.max.messages 条消息的时候，不会立马踢出ISR 集合，而是持续落后超过    replica.lag.time.max.ms 时间，才会被踢出，这样就能避免流量抖动造成的运维问题，因为follower 在下一次fetch的时候就会跟上leader， 这样就也不用对 topic 的写入速度做任何的估计喽。


# 参考

- [图解 kafka 的高可用机制](https://www.jianshu.com/p/ff296d51385a)