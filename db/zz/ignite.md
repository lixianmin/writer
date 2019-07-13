

----

#### 0x01 Abstract

1. IgniteCache中的基操好像都是原子的，包括getAndPutIfAbsent, getAndReplace这种，这些操作的共同点很可能是方法签名中throws TransactionException
2. 事务通过ignite.transactions().txStart()启动，这中间的基本操作的结果都是事务





-----

#### 0x02 Isolation Level

隔离级别定义了同步事务之间是如何处理同一个key。

1. READ_COMMITTED：在该隔离级别中，**Read数据不需要Lock**，事务也不会缓存读结果，因此同一个事务的多次读到的结果可能不同（NonRepeatable Reads），因此在**只读时事务不会抛出异常**。
2. REPEATABLE_READ：同一个事务中对同一个key的多次读写都是一致的
3. SERIALIZABLE ：读的时候与可重复读是一样的。在OPTIMISTIC模式下，竞争读失败的client会触发 TransactionOptimisticException



----

#### 0x03 Concurrency Mode

Ignite支持两种并发模型：悲观和乐观。

默认情况下，COMMIT消息默认情况下是单向发送的，不需要其它参与节点发送确认消息。如果必须要等待其它节点确认的话，需要通过setWriteSynchronizationMode()配置成同步commit和rollback模式



- PESSIMISTIC：悲观模式中，锁在第一次读写数据时获得，并一直保持到commit/rollback。悲观模式理论上只有两种不同的隔离级别，一种是**READ_COMMITTED只有Write需要锁，一种是SERIALIZABLE Read/Write都需要锁**：
  1. 悲观模式中的锁被primary nodes获得，并在prepare stage中promoted到back nodes
  2. READ_COMMITTED隔离级别中，**Read数据不需要Lock**（同时也不会缓存到事务中，NonRepeatable Reads），但是**第一次Write数据前需要获得Lock**。
  3. 在悲观模式中，REPEATABLE_READ/SERIALIZABLE隔离级别是一样的，用于读写都需要获取得锁的情况，事务在**第一次Read/Write数据之前需要获得Lock**，并可放心地将读到的数据缓存到事务中，因为其它的事务无法获得锁以修改数据。
  4. 在**悲观模式中，锁的获得顺序至关重要**，Ignite会严格按照用户指定的顺序获取锁，无法自动调整锁定顺序。从性能的角度考虑，**按partition将keys进行分组并依次锁定**会减少sequential network round-tips数

- OPTIMISTIC：乐观模式中，锁是在2PC中的prepare阶段获得的，如果事务没有尝试commit，而是直接rollback的话，则整个过程中就不会获取任何锁。
  1. 在调用commit()之前所有operations都不会分发到其它nodes，在此之前其它nodes对这些操作一无所知
  2. 只有到commit()时，才会尝试向其它参与节点发送PREPARE消息，以获得per-transaction的locks，等所有参与nodes返回OK后，会发送单向的COMMIT消息，但此不再等待其它节点的确认
  3. REPEATABLE_READ隔离级别中，事务会缓存读到数据，因此接下来的读操作都是local的。
  4. 乐观模式只有使用SERIALIZABLE隔离级别时才有可能失败，否则都会成功



----

#### 0x03 原子化模式（事务机制）

Ignite中支持两种不同的事务机制，一个二阶段提交的，一个是快照隔离的。

**快照隔离**的事务机制，即配置为TRANSACTIONAL_SNAPSHOT原子化模式的缓存，同时支持SQL事务以及键-值事务，这提供了多版本并发控制的机制（MVCC）。但这个在2.7版本中还不完善，不建议使用。

TRANSACTIONAL_SNAPSHOT只支持**默认的并发模型（悲观）和默认的隔离级别（可重复读）**



----

##### CacheAtomicityMode.ATOMIC

除了标准的TRANSACTIONAL之外，ignite还支持更轻量级的atomic模式，这种模式不支持分布式事务和分布式锁，其实就是禁用了事务和锁，此时性能会更高。

在任何不需要保证ACID-compliant事情的情况下，都建议使用atomic模式。



----

#### 0x09 References

1. [Apache Ignite事务架构：并发模型和隔离级别](https://my.oschina.net/liyuj/blog/1627248)
2. [Apache Ignite中文站](https://liyuj.gitee.io/doc/java/)
3. [Ignite在线文档](https://apacheignite.readme.io/docs/)
4. [Concurrency Modes and Isolation Levels](https://apacheignite.readme.io/docs/concurrency-modes-and-isolation-levels)
5. [Ignite事务接口](https://ignite.apache.org/releases/latest/javadoc/org/apache/ignite/transactions/Transaction.html)
6. [苏宁 11.11：基于 Apache Ignite 日均十亿数据对账实践应用](https://www.infoq.cn/article/PYlP47meo*y96NK5bjTP)
7. [Apache Ignite(一)：简介以及和Coherence、Gemfire、Redis等的比较](https://blog.csdn.net/xiaojin21cen/article/details/79072955)
8. 

