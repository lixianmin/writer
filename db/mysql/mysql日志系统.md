

---

#### 双1安全参数

1. 2PC： 先写redo log，处于prepare阶段，然后写binlog，最后commit事务
2. redo log与binlog都需要能够完整的重放数据库操作逻辑，先写哪一个都有问题，因此使用2PC解决两个log的状态不一致问题
3. **双1安全参数**：
   1. redo log 用于保证 crash-safe 能力。innodb_flush_log_at_trx_commit 这个参数设置成 1 的时候，表示每次事务的 redo log 都直接持久化到磁盘。这个参数我建议你设置成 1，这样可以保证 MySQL 异常重启之后数据不丢失
   2. sync_binlog 这个参数设置成 1 的时候，表示每次事务的 binlog 都持久化到磁盘。这个参数我也建议你设置成 1，这样可以保证 MySQL 异常重启之后 binlog 不丢失。



---

#### redo log

1. 重做日志，InnoDB特有
2. 物理日志，记录“在某个数据页上做了什么修改”
3. 循环写，顺序写，组提交



----

#### binlog

1. 归档日志，由mysql的server层实现，所有引擎都可以使用
2. 逻辑日志，记录的是这个语句的原始逻辑，比如“给 ID=2 这一行的 c 字段加 1 ”
3. 追加写