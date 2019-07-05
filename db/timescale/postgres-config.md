



------

#### 0x01 docker-compose配置

docker-compose.yml文件大体如下：

```yaml
version: '3'
services:
  master:
    restart: always
    image: timescaledb-postgis:latest-pg11
    network_mode: "host"
    ports:
      - "5432:5432"
    volumes:
      - /data/pgdata:/var/lib/postgresql/data:rw
      - /data/disk1:/data/disk1:rw
      - /data/disk2:/data/disk2:rw
```



1. 拉取image时，使用lastest-pg11；
2. `create tablespace disk1 location '/data/disk1';`



------

#### 0x02 postgresql.conf

该文件位于 /var/lib/postgresql 目录下，需要修改的细节如下：



```ini
# 默认位于系统盘上，由于系统盘比较小，所以需要将目录指向独立磁盘；
data_directory = ‘/data/disk1’

# 默认值是100，这个太小了，很多应用server连接池大小都会设置为100，所以需要改大一些
max_connections = 1000

# 默认是UCT，我们改成 PRC 或 Asia/Shanghai
log_timezone = 'PRC'
timezone = 'PRC'

# 默认256，改这个是因为spot_trade_detail建了9000个分区, drop table竟然失败
max_locks_per_transaction = 2048

# 需要打开一系列的日志配置
logging_collector = on
log_directory = 'log'
......

```



---

#### 0x03 时区设置



```mysql

-- 修改单个数据库的时区
alter database coinbene_archive set timezone = 'Asia/Shanghai';

-- 查看当前时间
select now();

-- 查看时区
show time zone;

-- 设置当前时区
set time zone 'PRC'；
```



------

#### 0x03 其它注意事项

1. create table建表时注意带tablespace
2. 特别是，需要归档的表，本来计划放到oss上面，但是发现在oss上创建tablespace似乎不成功

