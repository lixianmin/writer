

-----

##### 0x01 delete from导致db重启

risk-archive项目中，因为担心重复归档，计划使用如下语句将数量不对的区间数据删除，然后重新插入，结果发现该语句会导致db进程OOM。线上pg所在linux服务器内存32GB。

```mysql
delete from spot_trade_detail where trade_pair='LTCBTC' and trade_time >= '2018-11-13 04:56' and trade_time < '2018-11-13 04:57';
```

注：trade_time上是有索引的。



表现为随便执行一个select语句或\d+就会收到下面的错误，并且链接全部断开：


> WARNING:  terminating connection because of crash of another server process
DETAIL:  The postmaster has commanded this server process to roll back the current transaction and exit, because another server process exited abnormally and possibly corrupted shared memory.
HINT:  In a moment you should be able to reconnect to the database and repeat your command.




通过dmesg发现postgresql的进程被oom干掉了，消息如下（从微信中摘出来的消息，印象中是使用dmesg拿到的，不是很确定了）：

> [704432.751223] Killed process 19747 (postgres) total-vm:38433040kB, anon-rss:29852872kB, file-rss:0kB, shmem-rss:749048kB



通过不断回滚近期内的修改，发现执行本文开始处的`delete from xxx`语句时会导致db进程使用的内存量暴涨。

经查询，线上spot_trade_detail有超过上万的分区，这是由于建表时使用了add_dimension()语句。猜测oom是由于分区过多导致的。

```mysql
select add_dimension('spot_trade_detail', 'trade_pair', number_partitions => 300);
select add_dimension('spot_trade_detail', 'buy_user_id', number_partitions => 30);
```

移除该语句后发现`delete from xxx`语句不再导致db重启。

