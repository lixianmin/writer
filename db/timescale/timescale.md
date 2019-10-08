



---

#### 0x01 basics



| 命令                                                 | 描述     |      |
| ---------------------------------------------------- | -------- | ---- |
| select * from timescaledb_information.hypertable;    | 查表大小 |      |
| SELECT * FROM show_tablespaces('spot_trade_detail'); | 查表空间 |      |
|                                                      |          |      |



1. user_id类的，建立hash索引，要快很多；
2. `select create_hypertable('spot_trade_detail', 'trade_time', chunk_time_interval => interval '4 weeks');`  如果不能确定interval为多少的话，就选一个月吧，否则分表太多，读不动；
3. `create index spot_trade_detail_hash_buy_sell_order_id ON spot_trade_detail using hash((buy_order_id||sell_order_id));` 表达式索引是个好东西；



----

#### 0x02 示例

```sql

create table t (id serial, 
				uuid bigint not null, 
				create_time timestamp not null default now(),
				primary key (id, create_time),
				unique(uuid, create_time)
			   );

-- 1. 如果要在hypertable上使用unique或primary key的话，则需要在创建这两类索引的字段中加上create_time，否则创建hypertable会失败
-- 2. 计算chunk_time_interval的公式为： total_memory * 0.25/data_per_day，0.24是指只占用系统25%的内存，比如内存 16GB，一天收数据量为2G，则 chunk_time_interval = 16*0.25/2=2天
select create_hypertable('t', 'create_time', chunk_time_interval => interval '1 weeks');

-- 1. 按5分钟统计载客数量
-- 2. count(*)是聚集函数，因此它所count的是每一个bucket中的数据
-- 3. time_bucket的结果(five_min)可以作用group by和order by的条件
-- 4. +'2.5 minutes'是为了使用5min的middle结果，而不是left的结果
-- 5. 将utc的时间类型timetz使用localtime(按server的timezone设置)展示出来，因此需要强制类型转换为TIMESTAMP
SELECT time_bucket('5 minutes', timetz::TIMESTAMP) + '2.5 minutes' AS five_min, count(*)
  FROM rides
  WHERE pickup_datetime < '2016-01-01 02:00'
  GROUP BY five_min
  ORDER BY five_min DESC LIMIT 10;


-- 安装timescaledb CREATE EXTENSION IF NOT EXISTS timescaledb CASCADE;
-- 更改目录所有者 chown postgres:postgres /data/disk1/db
-- 更改目录权限 chmod 755 /data/disk1/db
-- chown postgres:postgres /data/disk2/db
-- chmod 755 /data/disk2/db
-- chown postgres:postgres /data/disk3/db
-- chmod 755 /data/disk3/db
-- chown postgres:postgres /data/disk4/db
-- chmod 755 /data/disk4/db
-- 创建tablespace CREATE TABLESPACE disk1 LOCATION '/data/disk1/db';
-- CREATE TABLESPACE disk2 LOCATION '/data/disk2/db';
-- CREATE TABLESPACE disk3 LOCATION '/data/disk3/db';
-- CREATE TABLESPACE disk4 LOCATION '/data/disk4/db';

DROP TABLE IF EXISTS public.tradelog;
CREATE TABLE public.tradelog(
    id bigserial,
    tp character varying(16) NOT NULL,
    tid character varying(32) NOT NULL,
    ts timestamp without time zone NOT NULL,
    p decimal(24, 14) NOT NULL,
    q decimal(18, 6) NOT NULL default 0,
    d char NOT NULL,
    exg character varying(16) NOT NULL,
    UNIQUE (tp, tid, ts)
) WITH (OIDS = FALSE) TABLESPACE disk1;

COMMENT ON COLUMN "public"."tradelog"."tp" IS 'base/quote';
COMMENT ON COLUMN "public"."tradelog"."exg" IS '交易所';
COMMENT ON COLUMN "public"."tradelog"."ts" IS '时间戳';
COMMENT ON COLUMN "public"."tradelog"."tid" IS '某个交易所的某个交易对内的tradelog唯一性id，如果第三方交易提供唯一性id，则使用这个id；否则根据ts+自增的时间戳或交易量hash得到组合的id';
COMMENT ON COLUMN "public"."tradelog"."p" IS '价格';
COMMENT ON COLUMN "public"."tradelog"."q" IS '数量';
COMMENT ON COLUMN "public"."tradelog"."d" IS '方向：b | s';

ALTER TABLE public.tradelog OWNER to postgres;

-- 按照时间和交易对分片  1周，300个chunks
<<<<<<< HEAD
SELECT create_hypertable('tradelog', 'ts', chunk_time_interval => interval '1 weeks');
=======
SELECT create_hypertable('tradelog', 'ts', chunk_time_interval => interval '4 weeks');
>>>>>>> 8b4ee714c23c79ce64b32cfdb8afdba74c8668ff
SELECT add_dimension('tradelog', 'tp', number_partitions => 300);

-- attach 多个磁盘，暂时4个
SELECT attach_tablespace('disk2', 'spot_trade_detail');
SELECT attach_tablespace('disk3', 'spot_trade_detail');
SELECT attach_tablespace('disk4', 'spot_trade_detail');

-- 查看tablespaces
select show_tablespaces('spot_trade_detail');

```



---

#### 0x03 函数



|                  |                               |      |
| ---------------- | ----------------------------- | ---- |
| interval '1 day' | 返回microseconds，86400000000 |      |
|                  |                               |      |
|                  |                               |      |



----

#### 0x04 聚合函数

聚合函数意味着用于group by中

| Name                                 | Notes                 |      |
| ------------------------------------ | --------------------- | ---- |
| first(value_column, time_column)     | first和last不使用索引 |      |
| histogram(value, min, max, nbuckets) | [min, max)+2          |      |
| time_bucket(bucket_with, time)       |                       |      |
|                                      |                       |      |





---

#### 0x09 References

1. [[TimescaleDB] API References](https://docs.timescale.com/v1.2/api#analytics)
2. [[TimescaleDB] Indexing Data](https://docs.timescale.com/v1.3/using-timescaledb/schema-management#indexing) 
3. 