----

0x01 主库delete from导致从库CPU爆满



配置：主库32C, 64GB，从库8C, 16GB

初衷是想归档线上的几个大表， 100GB ~ 300GB，这些表是分区的，但是没有主键，本来是想按时间逐批次delete，每次 100~300左右，但是发现启动delete后从库CPU很快爆满并卡死不可用。

我们的mysql在阿里云上，binlog_format=ROW

经分析，原因是**table没有主键**导致：当table没有主键时，主库的删除语句同步到从库后，为了定位到需要删除的数据行，对每一行数据都需要全表扫描，这是相当大的CPU浪费。

因此，当没有主键时，大表删除不能使用DML方式。



| 表名                     | 分区方式                                  | 处理方案   |
| ------------------------ | ----------------------------------------- | ---------- |
| trade_detail             | PARTITION BY RANGE (YEARWEEK(trade_time)) | 删除旧分区 |
| order_request_detail_his | PARTITION BY HASH (user_id)               |            |
|                          |                                           |            |



