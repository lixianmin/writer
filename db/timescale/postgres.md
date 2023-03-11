



---

#### 01 Abstract

##### 1 Basics

| Type            | Notes                       |
| --------------- | --------------------------- |
| int/integer     | 整数                        |
| numeric/decimal | 两者是一样的                |
|                 |                             |
| varchar(n)      | 限制最大长度为n的变长字符串 |
| text            | unlimited length字符串      |



##### 2 psql

|                      |                          |      |
| -------------------- | ------------------------ | ---- |
| \c coinbene_risk     | 切换数据库               |      |
| \d contract_position | 显示表信息               |      |
| \db+                 | 显示所有的表空间         |      |
| \dn+                 | 列出schema               |      |
| \dt contract*        | 显示匹配的表名称         |      |
| \du                  | 列出所有的数据用户和角色 |      |
| \l+                  | 列出当前的数据库         |      |
| \timing              | 打开查询时间             |      |
| \x                   | 打开/关闭竖行显示        |      |



##### 3 DataGrid

| short key       | notes                                 |      |
| --------------- | ------------------------------------- | ---- |
| cmd+shift+alt+B | 选中table/index，查看table/index的DDL |      |
| cmd+F6          | 选中table的任意部分，修改table        |      |
| cmd+shift+L     | 打开console                           |      |



----

#### 02 SQL语句



##### 0. Basics



1. 每张表都有一个叫ctid的东西，可以用作临时id，但这个并不是稳定的id，可能会变化
2. 使用hash索引有可能导入insert时cpu飙升到100%


| 语句                                                         |                                                  |
| ------------------------------------------------------------ | ------------------------------------------------ |
| select version();                                            | PostgreSQL版本                                   |
|                                                              |                                                  |
| SELECT pid, now()-pg_stat_activity.query_start AS duration, query, state  FROM pg_stat_activity WHERE now() - pg_stat_activity.query_start > interval '1 minutes'; | 查询耗时最长的进程，曾发现这是导致cpu 100%的元凶 |
| SELECT pid, now()-pg_stat_activity.query_start AS duration, query, state  FROM pg_stat_activity WHERE now() - pg_stat_activity.query_start > interval '1 minutes'  and query not like 'SET application_name%'  order by duration desc  ; | 专查大SQL                                        |
| select pg_cancel_backend(4850);                              | 按pid杀死锁连接                                  |
| select * from pg_indexes;                                    | 查询索引情况                                     |
| select * from pg_stat_activity;                              | 死锁查询                                         |
| select * from pg_tablespace;                                 | 查询当前的tablespace                             |
| select count(*) as max_count, a.pid,  b.query from pg_locks a inner join pg_stat_activity b on a.pid = b.pid  group by a.pid, b.query order by max_count desc limit 10; | 查询占锁最多的query                              |
| select relname, pg_size_pretty(pg_relation_size(oid)) from pg_class where relname like '%table_name%' order by relname; | 查询表的索引大小                                 |
|                                                              |                                                  |



``` mysql
-- 查询当前数据库下所有的大表，按从大到小排序，打印：表名，大小，表空间

SELECT table_schema || '.' || table_name 
AS table_full_name, pg_size_pretty(pg_total_relation_size('"' ||table_schema || '"."' || table_name || '"')) AS size, b.tablespace
FROM 
information_schema.tables a inner join pg_tables b on a.table_name = b.tablename
ORDER BY
    pg_total_relation_size('"' || table_schema || '"."' || table_name || '"')
DESC limit 30;


```



##### 1. Create Database

```mysql
CREATE DATABASE coinbene_archive
  WITH
  OWNER = postgres
  ENCODING = 'UTF8'
  LC_COLLATE = 'en_US.utf8'
  LC_CTYPE = 'en_US.utf8'
  TABLESPACE = pg_default
  CONNECTION LIMIT = 100;

-- 修改数据库的时区
alter database coinbene_archive set timezone = 'Asia/Shanghai';
```



##### 2. Create table

```mysql

create table cities (
	name text NOT NULL, -- 
  postal_code VARCHAR(9) CHECK (postal_code <> ''), -- 约束值不能是空字符串
  country_code CHAR(2) REFERENCES counties, -- 外键约束 
  PRIMARY KEY(country_code, postal_code) -- 联合主键
);

CREATE TABLE venues(
	venue_id SERIAL PRIMARY KEY,	-- 自增id
  type char(7) CHECK (type in ('public', 'private')) DEFAULT 'public'
);

```



##### 4. Insert

```mysql
-- 插入的同时可以返回一些数据
INSERT INTO venuse(name, postal_code, country_code)
VALUES ('Voodoo Donuts', '97205', 'us')
RETURNING venue_id;

-- 如果冲突则不插入，并且返回对应数据行
-- 这个方案是先select，如果没有数据的话则insert，但是有可以多个线程同时insert导致插入冲突
-- 1. 必须使用on conflict do update，经验证on conflict do nothing不能成功返回数据
-- 2. on conflict(...)中必须填入unique key
-- 3. returning * 后面必须带全的字段列表，不能只returning id，否则代码中也就只能接收到id
-- 4. executeAndReturnGeneratedKeys()的参数列表反而可以不写
insert into trade_step (uid, type, asset, balance, delta, t1, t2) 
values (?, ?, ?, ?, ?, ?, ?) 
on conflict(type, t1, uid, asset) do update set type= ? 
returning *


-- 防止重复插入
INSERT INTO t (id, name)
VALUES (123, 'apple')
ON CONFLICT (id) DO NOTHING

-- 从另一个表中选取数据并插入到新表中
insert into mobile_device(device_id) 
select distinct(device_id) from user_behaviour;

```



##### 9. 聚合函数

聚合表示统计，将数据按group by分组，每一个分组返回的只是本组的统计信息，因此select后面只能带聚合函数（以及用于分组的那个变量），而不能返回普通的表行的数值。

如果需要从多个维度聚合的话，需要使用group by 1, 2这种进行多次分组。



```mysql
-- having子句跟where类似，只不过它可以使用聚合函数作为过滤条件(而where则不能)
SELECT venue_id, COUNT(*)
FROM events
GROUP BY venue_id
HAVING COUNT(*) >= 2 AND venue_id IS NOT NULL;
```



##### 10. 时间

```mysql

-- timestamp类型
SELECT date_part('year', now());
SELECT date_part('date', now());
SELECT now() - INTERVAL '2 day';

SELECT time_bucket('1 hour', create_time) AS hour, COUNT(*)
FROM user_behaviour
WHERE create_time::date - '2019-05-06 12:00:00'::date = 2
GROUP BY hour;
```



##### 11. 账户维护

给大家一个线上的timescaledb数据库的只读连接账户，用于测试和只读项目上线：

用户名：risk_readonly

密码： cRqA8BJVE7X9vO5H (注意，这个文件的创建时间是在我来百度之前，跟百度没啥关系)



```mysql
CREATE ROLE risk_readonly WITH LOGIN PASSWORD 'cRqA8BJVE7X9vO5H' NOSUPERUSER INHERIT NOCREATEDB NOCREATEROLE NOREPLICATION VALID UNTIL 'infinity';

GRANT CONNECT ON DATABASE coinbene_archive TO risk_readonly;
GRANT CONNECT ON DATABASE coinbene_risk TO risk_readonly;
GRANT CONNECT ON DATABASE qps_data TO risk_readonly;


GRANT USAGE ON SCHEMA public TO risk_readonly;
GRANT SELECT ON ALL TABLES IN SCHEMA public TO risk_readonly;
GRANT SELECT ON ALL SEQUENCES IN SCHEMA public TO risk_readonly;
```



##### 12. 数据迁移（Copy）

```shell


PGPASSWORD=90-=op[] psql -U postgres -h localhost -d rc -c  "\COPY rc.spot_account_record FROM sar19a.csv CSV"



for((i=1;i<=39;i++));
do
	time PGPASSWORD="90-=op[]" psql -U postgres -h localhost -d rc -c  "\COPY rc.spot_account_record FROM /data/disk3/dump/fout.$i.csv CSV"
	echo "OK COPY rc.spot_account_record FROM /data/disk3/fout.$i.csv CSV"
done

```





----

#### 03 索引



1. 在先导列上的等值约束，加上第一个无等值约束列上的不等值约束，将被用于限制索引被扫描的部分
2. hash索引仅用于等值判断



```mysql
-- 在name列创建hash索引
CREATE INDEX idx_user_name ON user USING HASH(name);
DROP INDEX idx_user_name;

```



---

#### 04 Select

##### 01. 常用

```mysql
-- 1. where子句中出现的表名字必须 出现 在from子句中
-- 2. random()返回[0, 1)间的随机数
with t as (
	select min(id) as min_id, max(id) as max_id from conditions
)
select * from conditions c, t where c.id > t.min_id + (t.max_id - t.mid_id) * random() limit 3;

-- 1. 窗口函数，带over(partition by leverage)
select time_bucket('1 day', create_time)     as time,
       leverage                              as 杠杆倍数,
       count(*)                              as 开仓用户数,
       count(*) * 1.0 / sum(count(*)) over() as open_ratio
from contract_position
where $__timeFilter(create_time)
  and contract = '$contract'
group by 1, 2;

-- 显式锁
select * from t limit 10 for share;

-- nowait：当无法获取所有行锁时直接返回error，可用于秒杀场景，提高并发度
select * from t limit 10 for update nowait;

```



##### 02 交易手续费排名前800

```mysql
-- 从trade_detail中查询交易手续费排名前800的人
-- 手续费分buy_fee与sell_fee费，需要分别统计后再加起来
-- 由quote_asset不一样，需要按汇率折算后统一排名
with buyers as (
  select buy_user_id, sum(
  case
  	when quote_asset = 'BTC' then buy_fee * 10040
  	when quote_asset = 'ETH' then buy_fee * 220
  	when quote_asset = 'USDT' then buy_fee
  	else 0
  end
) as total_buy_fee from spot_trade_detail where trade_time >= '2019-01-01' group by buy_user_id order by total_buy_fee desc limit 800
),

sellers as (
  select sell_user_id, sum(
  case
  	when quote_asset = 'BTC' then sell_fee * 10040
  	when quote_asset = 'ETH' then sell_fee * 220
  	when quote_asset = 'USDT' then sell_fee
  	else 0
  end
) as total_sell_fee from spot_trade_detail where trade_time >= '2019-01-01' group by sell_user_id order by total_sell_fee desc limit 800
)
-- 因为使用了full join，因此会存在buy_user_id为null或sell_user_id为null的情况，因此需要使用coalesce()函数，参数1为null的时候就选第2个参数；sum中通过类似的方式将null的fee转为0值
select coalesce(buy_user_id, sell_user_id) as user_id, sum(coalesce(total_buy_fee, 0) + coalesce(total_sell_fee, 0)) as total_fee from buyers full join sellers on buy_user_id = sell_user_id group by user_id order by total_fee desc limit 800;
```



取每小时的手续费

```mysql
with big as (
  select
    td.quote_asset,
    td.buy_fee,
    td.sell_fee,
    cd.buy_fee as cd_buy_fee,
    cd.sell_fee as cd_sell_fee
  from spot_trade_detail td inner join spot_coni_discount cd
  	on cd.id = concat(buy_order_id, sell_order_id)
  where td.trade_time >= '2019-08-01 00:00:00'
    and td.trade_time < '2019-08-02 01:00:00'
    and cd.create_time >= (timestamp '2019-08-01 00:00:00' - interval '30 minute')
    and cd.create_time < (timestamp '2019-08-02 01:00:00' + interval '30 minute')
)

(
  select
    quote_asset,
    sum(case when cd_buy_fee < 0 then buy_fee else 0 end) as buy_fee,
    sum(case when cd_sell_fee < 0 then sell_fee else 0 end) as sell_fee
  from big
  group by quote_asset
 )
 union
 (
   select
     'CONI',
     sum(case when cd_buy_fee >= 0 then cd_buy_fee else 0 end),
     sum(case when cd_sell_fee >= 0 then cd_sell_fee else 0 end)
   from big
 );

```



```mysql
with
td as (
	select *
  from spot_trade_detail
  where trade_time >= '2019-07-01 00:00:00' and trade_time < '2019-07-01 01:00:00'
),
cd as (
  select *
  from spot_coni_discount
  where create_time >= (timestamp '2019-07-01 00:00:00' - interval '30 minute') and create_time < (timestamp '2019-07-01 01:00:00' + interval '30 minute')
),
price_map as (
	(
    select base_asset, min(actual_price) as actual_price
    from td
    where quote_asset = 'USDT'
    group by trade_pair
  )
  union
  (
  	select 'USDT', 1
  )
)

select
td.trade_pair,
sum(case when cd.buy_fee < 0 then td.buy_fee else 0 end) as buy_fee,
sum(case when cd.sell_fee < 0 then td.sell_fee else 0 end) as sell_fee,
sum(amount) as amount,
sum(quantity) as quantity,
avg(td.actual_price) as actual_price,
sum(amount) * pm.actual_price as usdt_price,
sum(amount) / sum(quantity) as avg_price

from td inner join price_map pm on td.quote_asset = pm.base_asset
	 inner join cd on cd.id = concat(td.buy_order_id,sell_order_id)
group by td.trade_pair, pm.actual_price
order by td.trade_pair;
```





