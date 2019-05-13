



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



##### 1. Create table

```mysql

CREATE TABLE cities (
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



##### 2. Insert

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











