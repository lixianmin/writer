



----

#### 0x01 安装

1. brew services restart grafana
2. 启动后，本机地址为： http://localhost:3000
3. 默认的用户名密码是 admin : admin，密码在第一次登陆时会被要求强制修改
4. 安装Zabbix插件



----

#### 0x02 查询示例



```mysql
SELECT
  -- 这里as后面的名字必须叫“time”，是传递给grafana的名字，其它的不接受
  time_bucket('1 day',"create_time") AS "time", avg(humidity) AS "avg_temperature"
FROM conditions
WHERE
  $__timeFilter(create_time)	-- 这里使用_timeFilter是为了转化成grafana中选择的时间区间
GROUP BY 1
limit 100;
```



grafana中查询是支持自定义变量的：

1. 自定义变量支持字符串内插，比如 ` interval '$deltaDays days'`，如果detalDays=1，则会翻译成 `interval '1 days'`
2. 如果自定义变量中间有空格的话，在定义时使用双引号引起来，比如： “1 days”, "7 days", "30 days"



----

#### 0x03 显示格式

1. 表单里数字以Number格式显示时，可能会变成科学计数法，如果想显示全，可以考虑改成String
2. 

