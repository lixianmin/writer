



----

#### 0x01 安装

1. brew services restart grafana
2. 启动后，本机地址为： http://localhost:3000
3. 默认的用户名密码是 admin : admin，密码在第一次登陆时会被要求强制修改
4. 安装Zabbix插件



----

#### 0x02 查询示例



```mysql
 -- Time series模式下，select参数：time时间轴，metric度量维度（对应折线），其它的都需要是聚集函数
 
SELECT
  -- 这里as后面的名字必须叫“time”，是传递给grafana的名字，其它的不接受
  time_bucket('1 day',"create_time") AS "time", 
  avg(humidity) AS "avg_temperature"
FROM conditions
WHERE
  $__timeFilter(create_time)	-- 这里使用_timeFilter是为了转化成grafana中选择的时间区间
GROUP BY 1
limit 100;
```



grafana中查询是支持自定义变量的：

1. 自定义变量支持字符串内插，比如 ` interval '$deltaDays days'`，如果detalDays=1，则会翻译成 `interval '1 days'`
2. 如果自定义变量中间有空格的话，在定义时使用双引号引起来，比如： “1 days”, "7 days", "30 days"
3. 面板的时间设置里选browser时间，然后sql中减去8小时

```mysql
SELECT
  $__timeGroup(ts,$timeInterval) - interval '8 hour' AS "time",
  sum(fee_total) AS "fee_total"
FROM gf.trade_summary
WHERE
  $__timeFilter(ts - interval '8 hour') AND
  exg = 'coinbene' AND
  tp IN ($tp)
GROUP BY 1
ORDER BY 1
```



----

#### 0x03 显示格式

1. 表单里数字以Number格式显示时，可能会变成科学计数法，如果想显示全，可以考虑改成String；
2. 时间那一列的格式，需要手动把`Time`改为`time`，否则显示怪怪的；



---

#### 0x04 Tips

1. 如果有多个开发环境间需要创建一样的panel的话，可以使用Panel JSON功能；
2. 权限控制：每一个Dashboards(Folder)设置中，可以通过把**Viewer删除 + Team的方式**进行权限控制；
3. 时间面板中，可以设置成间隔性自动刷新；
4. Explorer可以进行简单查询统计，但是不支持导出CSV；
5. 直接Edit SQL的方式往往更灵活一些；
6. 



---

#### 0x09 References

1. [Using MySQL in Grafana](https://grafana.com/docs/features/datasources/mysql/)
2. 

