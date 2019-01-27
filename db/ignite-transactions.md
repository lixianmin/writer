

----

#### 0x01 Concurrency Mode

Ignite支持两种不同并发模型：[悲观](https://apacheignite.readme.io/docs/transactions#pessimistic-transactions)和[乐观](https://apacheignite.readme.io/docs/transactions#optimistic-transactions)。



------

#### 0x02 悲观模式

悲观模式理论上只区分两种不同的隔离级别，即READ_COMMITTED和REPEATABLE_READ/SERIALIZABLE，它们的**区别在于：Read数据时是否需要获取锁**。注：Write是一定要获取锁的。

悲观模式中的锁，一旦获取后，会一直保持到commit/rollback。



下面的代码示例展示了读提交并且带有[死锁处理](https://apacheignite.readme.io/docs/transactions#deadlock-detection)的悲观事务:

```java
try (Transaction tx = ignite.transactions().txStart(TransactionConcurrency.PESSIMISTIC, TransactionIsolation.READ_COMMITTED, TX_TIMEOUT, 0)) {

    Account acct = cache.get(acctId);
    assert acct != null;

    ...

    // Deposit into account.
    acct.update(amount);

    // Store updated account in cache.
    cache.put(acctId, acct);

    tx.commit();
} catch (CacheException e) {
    if (e.getCause() instanceof TransactionTimeoutException &&
        e.getCause().getCause() instanceof TransactionDeadlockException)

        System.out.println(e.getCause().getCause().getMessage());
}
```



----

#### 0x03 乐观模式

乐观并发模型延迟了锁的获取，更适合于资源争用较少的应用。乐观支持三种隔离级别，由于Read Commited和Repeatable Read不会检查数据是否改变，没有了数据原子性保证，因此不建议在生产中使用。

只建议使用Serializable的乐观事务：

```java
for (int i= 0; i< 10; i++) {
    try (Transaction tx = ignite.transactions().txStart(TransactionConcurrency.OPTIMISTIC, TransactionIsolation.SERIALIZABLE)) {

        Account acct = cache.get(acctId);

        assert acct != null;

        ...

        // Deposit into account.
        acct.update(amount);

        // Store updated account in cache.
        cache.put(acctId, acct);

        tx.commit();

        // Transaction succeeded. Exiting the loop.
        break;
    } catch (TransactionOptimisticException e) {
        // Transaction has failed. Retry.
    }
}
```



------

#### 0x09 References

1. [Apache Ignite事务架构：并发模型和隔离级别](https://my.oschina.net/liyuj/blog/1627248)
2. [Concurrency Modes and Isolation Levels](https://apacheignite.readme.io/docs/concurrency-modes-and-isolation-levels)
3. [Ignite事务接口](https://ignite.apache.org/releases/latest/javadoc/org/apache/ignite/transactions/Transaction.html)