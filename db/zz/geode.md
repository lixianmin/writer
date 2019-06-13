

|                            |                                                              |
| -------------------------- | ------------------------------------------------------------ |
| Cache Client               | 接入到Gemfire分布式系统的客户端，负责管理本地缓存的生命周期  |
| Cache Server               | Gemfire分布式系统的服务器节点                                |
| Client Caching             | Gemfire分布式系统是一个网格结构，所有的Peer直接互相连接。Gemfire也支持客户端，客户端有他们自己的本地数据缓存，他们能够更新他们的本地缓存，通过注册服务器来更新改变的数据 |
| CQ                         | Gemfire分布式系统能够为客户端提供持续查询服务，一旦服务器内的数据发生变化，可以立刻同步到客户端 |
| Distributed Locator        | 注册客户端和服务器成员，使双方能够互相发现，同时可以提供一定的负载均衡功能 |
| Distributed Lock           | 通过特定的分布式管理器定义一个专有资源，跨整个分布式系统锁定任意的名称。 |
| Distributed Member         | 在Gemfire分布式系统中，加入到集群中的某个成员，有多种角色。  |
| Distributed Transaction    | 在Gemfire分布式系统中，在多个网络节点上访问或更新数据，跨节点协调事务提交或回滚。 |
| Dynamic Regions            | 分布式成员，如P2P                                            |
| Gateway                    | 外部分布式系统的本地代理器，主要作为跨WAN网同步数据的出口节点 |
| Gemfire Distributed System | 运行在Gemfire上的VMs组成了分布式系统。每一个VM都作为一个Gemfire对等体存在。启动Gemfire对等体，在每个VM对等体上你创建了一个缓存。每一个缓存管理到其它VM对等体的连接，通过UDP多播或TCP位置服务，互相发现 |
| Region                     | Region是一个分布式系统之上的抽象概念，一个Region允许你在系统的多个VM上存储数据，不用考虑数据存储在哪个对等体上。Region给你一个map接口能透明的从合适的VM上获取数据。这个Region类扩展了java.util.Map接口，同时也支持查询和事务。这个是Gemfire的核心中核心，一切的一切始于此。 |
| Replicated Region          | Replicated Region保存着所有分区的数据拷贝                    |
| Partitioned Region         | Partitioned Region只保存一部分分区的数据拷贝                 |
| Shared-Nothing Persistance | Gemfire支持”非共享“持久化，每一个peer持久化数据到本地磁盘，Gemfire持久化允许在磁盘维护一份配置的数据拷贝 |



sudo docker run -it --net=host -e "CONFIG_URI=https://raw.githubusercontent.com/apache/ignite/master/examples/config/example-cache.xml" apacheignite/ignite



----

#### References

1. [Apache Ignite 初探](https://mp.weixin.qq.com/s?__biz=MjM5MDE0Mjc4MA==&mid=401190345&idx=1&sn=b3f16fe6d2ebe880433a91582bf52ea4&scene=27#wechat_redirect)
2. [Gemfire - 分布式缓存利器](https://www.jianshu.com/p/ae2b23ca7535)
3. [Apache Ignite核心特性介绍(2.1.0版)](https://www.zybuluo.com/liyuj/note/293596)
4. [常用内存数据库介绍](https://blog.csdn.net/jackxinxu2100/article/details/6764530)
5. [System Properties Comparison Geode vs. Ignite vs. Redis](https://db-engines.com/en/system/Geode%3BIgnite%3BRedis)
6. [GridGain vs Hazelcast Benchmarks](https://www.gridgain.com/resources/benchmarks/gridgain-vs-hazelcast-benchmarks)