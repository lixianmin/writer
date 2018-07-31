

------

#### 0x01 redis命令行

1. redis-cli 客户端
2. 



| Command             | Description         |
| ------------------- | ------------------- |
| dbsize              | key总数             |
| expire key seconds  | 设置key过期时间     |
|                     |                     |
| keys *              | 查看所有keys        |
| object encoding key | 测试编码            |
| ttl key             | 测试key剩余存活时间 |
| type key            | 测试类型            |



---

#### 0x02 string

| Command                                     | Description |
| ------------------------------------------- | ----------- |
| append key | 向string尾部追加 |
| del key |  |
| exists key |  |
| get key                                     | 取值        |
| getset key value | 取值并设值 |
| set key val [ex seconds] \[px milliseconds] \[nx\|xx] | 1. ex seconds 是秒级过期时间<br>2. px milliseconds是毫秒级过期时间<br>3. nx是key必须不存在才可以设置成功，用于添加<br>4. xx是key必须存在才可以设置成功，用于更新 |
| strlen | string长度 |
|  |  |
| decr key | integer - 1 |
| decrby key delta | integer - delta |
| incr key | integer + 1 |
| incrby key delta | integer + delta |
| incrbyfloat key delta |  |
| mget key [key2...] | 同时获取多个key |
| mset key value [key2 value2 ...] | 同时设置多个key |
| getrange key 0 0 | 获取一个区间，区间范围左闭右闭 |
| setrange key 0 "value with space" | 从某个offset开始设值，有则覆盖 |
|  |  |
|  |  |



----

#### 0x03 hash




| Command                                 |      | Description |
| --------------------------------------- | ---- | ----------- |
| hset key field value                    |      | 设置值      |
| hget key field                          |      | 获取值      |
| hdel key field [field,...]              |      | 删除值      |
| hlen key                                |      | 集合大小    |
|                                         |      |             |
| hmset key field1 value1 [field2 value2] |      | 设置多个值  |



------

#### 0x04 list

​    



| Command                       |      | Description |
| ----------------------------- | ---- | ----------- |
| rpush key value[, value, ...] |      |             |



------

#### 0x03 References

1. [Index Merge Optimization](https://dev.mysql.com/doc/refman/8.0/en/index-merge-optimization.html)
2. 