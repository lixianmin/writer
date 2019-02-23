

----

#### 0x01 Abstract



##### Consistency一致性

所有的clients读的都是最新的write结果，或者读到error，clients可以根据读到的结果采取相应的措施。

1. 对于service node而言，它们是否拥有同样的数据并不重要，重要的是clients从所有service node上读到的数据必须是相同的
2. 当发生网络分区时，如果service node之间有数据没有同步完成，此时**从这些service node上读数据时必须返回error**，只有这样才能保证一致性
3. 一致性指的就是**ACID，事务**



##### Availability可用性

**非故障**node必须在**合理的时间**内给出**合理的响应**，不能返回error。相反，在Consistency中是允许返回error的。

1. 合理的时间**是指响应不能超时**
2. **合理的响应**是指：service node返回的可能是**最新（正确）的或旧（不正确）的数据**，但不能返回error。选择AP模式的clients需要确认自己可以接受相对旧一些的数据



##### Partition Tolerance分区容错性

当发生网络分区时，系统**仍然必须正常履行职责**。

1. **正常履行职责**承接是指做到Consistency或Availability
2. 这里的Partition是指的网络分区，是指代网络故障，比如：丢包、网络中断、拥塞等



<img src="https://raw.githubusercontent.com/lixianmin/writer/master/db/images/cap-theorem.png?raw=true"  style="zoom:70%" />



---

#### 0x02 适用场景



三类系统的适用性分析：

| 类型                     | 举例                                 | 描述                 |
| ------------------------ | ------------------------------------ | -------------------- |
| CP（一致性、分区容错性） | 数据一致性高，通常性能比较差         | Redis, MongoDB       |
| AP（可用性、分区容错性） | 性能好，数据一致性差                 | NoSQL，多数网站架构  |
| CA （一致性，可用性）    | 单点服务，不是分布式系统，可扩展性差 | RDBMS：MySQL, Oracle |



1. CAP理论探讨的是分布式系统，而不是单节点系统。单节点系统不存在网络分区故障，因此不需要分区容错，可以做成CA系统，可以同时达到强一致和可用性。
2. CAP强调share data，没有数据共享的集群（比如memcache cluster）并不是CAP讨论的范围。
3. CAP强调数据的read/write，没有读/写的系统比如ZooKeeper中的选举系统就不在讨论范围内。
4. CAP**不能同时做到CA的前提是出现了网络分区故障**。在没有网络故障时，分布式系统正常运行，此时可以是同时保证CA的



---
#### 0x03 常见系统的CAP分析



1. Ignite：

   - 在TRANSACTIONAL atomicity mode下，Ignite是CP系统，此时是否支持ACID是**可选**的。
   - 在ATOMIC atomicity mode下，Ignite是一个AP系统，此时系统运行速度快，但放弃了一至性。


-----

#### 0x09 References

1. [The CAP Theorem and Apache Ignite-  CP or AP?](https://www.gridgain.com/resources/blog/cap-theorem-and-apache-ignite-cp-or-ap)
2. [分布式系统理论（一）：CAP定理](https://my.oschina.net/lhztt/blog/915533)
3. [redis笔记 （番外篇）——从RDBMS到NoSQL的架构演化及CAP原理](https://my.oschina.net/happyBKs/blog/1557597)