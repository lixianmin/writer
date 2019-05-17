



---

#### 0x01 类型导论



| Type            | Notes                       |
| --------------- | --------------------------- |
| int/integer     | 整数                        |
| numeric/decimal | 两者是一样的                |
|                 |                             |
| varchar(n)      | 限制最大长度为n的变长字符串 |
| text            | unlimited length字符串      |



----

#### 0x02 SQL语句



##### 0. Basics



| 语句                            |                 |
| ------------------------------- | --------------- |
| select version();               | PostgreSQL版本  |
|                                 |                 |
| select pg_cancel_backend(4850); | 按pid杀死锁连接 |
| select * from pg_indexes;       | 查询索引情况    |
| select * from pg_stat_activity; | 死锁查询        |
|                                 |                 |
|                                 |                 |



##### 1. Create table

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



##### 2. Select

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
select * from t limit 10 for update;

```



##### 3. Insert

```mysql
INSERT INTO venuse(name, postal_code, country_code)
VALUES ('Voodoo Donuts', '97205', 'us')
RETURNING venue_id; -- 插入的同时可以返回一些数据



```



##### 8. 聚合函数

聚合表示统计，将数据按group by分组，每一个分组返回的只是本组的统计信息，因此select后面只能带聚合函数（以及用于分组的那个变量），而不能返回普通的表行的数值。

如果需要从多个维度聚合的话，需要使用group by 1, 2这种进行多次分组。



```mysql
-- having子句跟where类似，只不过它可以使用聚合函数作为过滤条件(而where则不能)
SELECT venue_id, COUNT(*)
FROM events
GROUP BY venue_id
HAVING COUNT(*) >= 2 AND venue_id IS NOT NULL;
```



##### 9. 时间

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



----

#### 0x03 索引



1. 在先导列上的等值约束，加上第一个无等值约束列上的不等值约束，将被用于限制索引被扫描的部分
2. hash索引只支持等值判断



```mysql

-- 在title列创建hash索引
CREATE INDEX events_title ON events USING HASH(title);

DROP INDEX events_title;
```











