

---

#### 0x01 事务隔离级别



| 隔离级别                     | 读锁                             | 写锁                       |
| ---------------------------- | -------------------------------- | -------------------------- |
| 未提交读（Read uncommitted） | 无锁                             | 行级共享锁                 |
| 已提交读（Read committed）   | 行级共享锁，读完就释放（短期锁） | 行级排他锁，事务结束才释放 |
| 可重复读（Repeatable read）  | 行级共享锁，事务结束才释放       | 行级排他锁，事务结束才释放 |
| 可串行化（Serializable ）    | 表级共享锁                       | 表级排他锁                 |



当多个clients使用不同的隔离级别操作同一数据的时候，不同的隔离锁都能有条不紊的正常工作，所不同的是：

1. 强隔离锁排他性更强，隔离性更好，更难获得，性能越差；
2. 弱隔离锁排他性更差，隔离性更差，也更容易获得，性能越好。



未提交读是一个没用的东西，对我们有用的事务隔离级别有三种：ReadCommited, RepeatableRead与Serializable：

1. **读锁即共享锁，写锁即排他锁**，在这里是可以互换的。大家都是成年人，这一点还是要先达成共识
2. ReadCommited, RepeatableRead使用的是**行级锁，而Serializable使用的是表级锁**
3. ReadCommited与RepeatableRead的区别是**什么时候释放读锁**，前者是读完就释放，后者是等事务结束才释放
4. 就是**因为使用了行级锁，所以才会出现幻读的现象**，因为你只锁定**所有行**这个概念只是当前的所有行，未来有新插入的行时，“所有行”的个数已经变了。



| 隔离级别                     | Dirty Read | NonRepeatable Read | Phantom Read |
| ---------------------------- | ---------- | ------------------ | ------------ |
| 未提交读（Read uncommitted） | √          | √                  | √            |
| 已提交读（Read committed）   | x          | √                  | √            |
| 可重复读（Repeatable read）  | x          | x                  | √            |
| 可串行化（Serializable ）    | x          | x                  | x            |



1. 未提交读(Read Uncommitted)：允许脏读，也就是可能读到其他会话中**未提交**事务修改的数据，要知道其它事务可能会回滚
2. 提交读(Read Committed)：只能读取到已经提交的数据，Oracle等多数数据库默认都是该级别 (不重复读)
3. **可重复读(Repeated Read)**：可重复读。在同一个事务内的查询都是事务开始时刻一致的，InnoDB默认级别。在SQL标准中，该隔离级别消除了不可重复读，但是还存在**幻读**（第一个事务对一个表中的数据进行了修改，这种修改涉及到表中的全部数据行。同时，第二个事务也修改这个表中的数据，这种修改是向表中插入一行新数据。那么，以后就会发生操作第一个事务的用户发现表中还有没有修改的数据行，就好象发生了幻觉一样）
4. 串行读(Serializable)：完全串行化的读，每次读都需要获得表级共享锁，读写相互都会阻塞





---

#### 0x09 References

1. [浅谈事务隔离级别](https://zhuanlan.zhihu.com/p/34742600)
2. 