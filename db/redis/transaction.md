

----

#### 0x01 ACID

Redis的事务通过multi, exec, discard, watch命令支持，redis只能支持ACID中的ACI三个特性，无法支持D。2.6之后的redis可以考虑使用lua script实现事务。



##### Atomic原子性

Redis的每条命令都是原子性的，但这并无法保证同一client发起的多条命令联合起来也保证原子性。

通过multi启动事务后，所有后续指令queue到redis服务器，在收到exec之前并不执行。直到接收到exec后，所有命令一次性执行，中间不得插入其它client的命令。但这仍然无法保证这一批命令是原子性的。

在multi到exec的这段时间内，其它clients可以正常向server发送命令并执行。如果事务的执行需要依赖某些key的数值稳定的话，则需要使用watch配合，一旦在[multi, exec]中间有client修改了某些key的数据，则放弃事务执行。

Redis的事务并不支持回滚，**从这一点上讲，redis并不算是支持了原子性**。



#### Consistency一致性

Redis操作在各种条件下都是一致的



#### Isolation隔离性

Redis 是单进程程序，并且它保证在执行事务时，不会对事务进行中断，事务可以运行直到执行完所有事务队列中的命令为止。因此，Redis 的事务是总是带有隔离性的。



##### Durability持久性

因为事务不过是用队列包裹起了一组 Redis 命令，并没有提供任何额外的持久性功能，，所以事务的持久性由 Redis 所使用的持久化模式决定。然而，很遗憾，在无论是内存模式、RDB模式、AOF模式，都无法保证持久性。





---

#### 0x02 事务 vs. 锁

1. 事务是由DB服务器实现的，是官方约定，可能需要也可能不需要client的主动配合。
2. 锁是同一进程的各threads间的私下约定，是民间约定，自由但要求所有的thread讲规矩，一旦thread不遵守约定偷偷改数据，则锁就失去了意义。
3. 事务的隔离性不是简简单单的相互隔离，而是由Isolation Level决定
4. 每一条sql语句，就像是一条redis命令，都是原子的，要么执行成功，要么没执行成功被回滚到初始状态。



---

#### 0x09 References

1. [Redis命令参考：事务](http://redisdoc.com/topic/transaction.html)
2. [Redis 设计与实现（第一版）](https://redisbook.readthedocs.io/en/latest/index.html)
3. 