



----

#### 0x01 概述

1. 亚太东北1： https://ide2-ap-northeast-1.data.aliyun.com/
2. 周期任务中失败的实例可以右键选择重跑，**重跑时涉及到的各时间参数**不变
3. 一键生成目标表：
   1. 因为maxcompute使用的hive，支持的数据类型跟mysql不太一样，因此需要一些数据类型上的转换， 如果自己定义目标表的话，可能会定义varchar这类不支持的类型，因此还是使用一键生成目标表更方便一些；
   2. 但是，**datetime字段，请影射成string类型**，否则由于dataworks所在时区不是东8区，数据写到maxcompute中后，会被修改成dataworks服务器所在的时区时间 (改成timestamp表示不支持，需要设置setproject odps.sql.type.system.odps2=true; 然而我并知道在哪里设置；改成bigint后发现数值上跟timestamp是一样子的是毫秒级时间戳，但是时区也被调整过了，数值不对)
4. insert overwrite：insert前先清空旧表中的数据，然后再insert。此时如果整个操作时间过长，比如insert耗时30分钟，那在这30分钟内只能读取到半殘的数据
5. 目前是希望 mysql同步到 ODPS中 ，mysql其中有一列作为分区字段，来实现二级的动态分区写入(ODPS表已经有了一级分区dt)。目前dataworks同步日志 ，写入到ODPS目的表还不支持 动态分区形式的。  可以先将数据同步到一个 原始表 （如非分区表），然后再通过 insert overwrite的方式导入到 最终的业务表 来实现下。
6. 



----

#### 0x02 SQL语句



##### 查询表

```mysql
# 查询表占用的size
desc t;

# 对于分区表，查询时必须where中加上分区条件，否则拒绝查询
select * from account_history where ds = 20190616 limit 10;


```



##### 删除表

```mysql
# 删除表
drop table t0;

# 清空非分区表
truncate table t1;

# 查询分区信息
show partitions t2;

# 删除分区
alter table t2 drop if exists partition (ds='20190619');
```



------

#### 0x03 FAQ



##### 同步方案通用设置

1. 时间属性中选择"发布后即时生效"，这样才会立即在"运维中心"中生成周期任务的实例
2. "出错重试"勾选上，这样才能重试
3. "错误记录数超过" 0 条，则任务结束，表示严格不允许脏数据存在



##### 小表同步方案

1. 清理规则选insert overwrite：对于src表会update的情况，通常是小表，可以每次同步都完全覆盖
2. 一键生成目标表的时候，记得：
   1. 将datetime类型改为string
   2. 将PARTITIONED BY (ds STRING) 删除



##### 大表同步方案

1. 清理规则选insert overwrite：对于src表只insert的情况，通常是历史记录表，并将同步时间设置为前一天
2. 设置分区信息 ds=${ds}
3. 生成后，使用show partitions t; 查看创建的第一个表的ds时间是多少，比如 2019062216，那么就可以创建对应的另一个一次性任务，并将区间时间改为: `date_format(trade_time, '%Y-%m-%d %H:%i') < '2019-06-22 16:00'`，然后手动执行之
4. 



```shell
# 按小时调度输入参数设置：
# 1. datetime=$[yyyy-mm-dd - 1/24 -1/24] 这里后面的 -1/24 是指要减去1小时，这样子，因为我们总是归档前一小时的数据，因此如果是 2019-05-06 00:00:00归档的话，需要归档的真实时间是 2019-05-05 23:00
# 2. 因为我们的服务器部署在亚太东北1,而这里的时区为GMT+9,相对于我们的数据时区GMT+8多一个小时，因此参数设置中需要再多减一个小时，即datetime到last_half_hour中所有的 -1/24 都是为了调整这个时区
last_h2_day=$[yyyy-mm-dd - 2/24 -1/24] last_h2=$[hh24 - 2/24 -1/24] last_h1_day=$[yyyy-mm-dd - 1/24 -1/24] last_h1=$[hh24 - 1/24 -1/24] ds=$[yyyymmddhh24 - 2/24 -1/24]

last_h2_day=$[yyyy-mm-dd - 2/24 +15/24] last_h2=$[hh24 - 2/24 +15/24] last_h1_day=$[yyyy-mm-dd - 1/24 +15/24] last_h1=$[hh24 - 1/24 +15/24] ds=$[yyyymmddhh24 - 2/24 +15/24]

# 每小时归档数据过滤条件：
# 不能直接定义一个变量 last_time=$[yyyy-mm-dd hh24:mi]，必须将日期与小时分隔到两个变量时，并且在sql语句中使用时拿字符串拼写起来 ---- 如果真那样定义的话，在sql中得到last_time中间的空格将会被过滤掉

trade_time >= '${last_h2_day} ${last_h2}' and trade_time < '${last_h1_day} ${last_h1}'
```



##### 重建表结构

1. `drop table t;` 删除原表；
2. 在"同步任务"中使用"一键生成目标表"的功能，重新生成表结构；
3. 因为"同步任务"是周期性执行的，删除原表的时候同步任务通常也不会在运行，因此不需要暂停同步任务；



----

#### 0x09 References

1. [Dataworks参数配置常见问题](https://www.alibabacloud.com/help/zh/doc-detail/74450.html#title-jmr-imt-rzr)
2. [Dataworks数据增量同步](https://www.alibabacloud.com/help/zh/doc-detail/87157.htm?spm=a2c63.p38356.b99.70.36bd26458g9WQr)
3. [2018年12月24日-MaxCompute支持时区配置](https://www.alibabacloud.com/help/zh/doc-detail/44035.htm#h2-url-3)
4. [输出到动态分区（DYNAMIC PARTITION）](https://help.aliyun.com/document_detail/73779.html?spm=a2c4g.11186623.2.12.63ab156c0yyYmO)
5. [配置Endpoint](https://help.aliyun.com/document_detail/34951.html?spm=5176.11065259.1996646101.searchclickresult.2a82262e42NboG)
6. 