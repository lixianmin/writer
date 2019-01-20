

------

#### 0x01 redis命令行

1. redis-cli 客户端
2. 很多方法都带一个key参数，这个应该理解为OO中的this
3. range类操作在redis中的区间都是左闭右闭的



| Command             | Description             |
| ------------------- | ----------------------- |
| dbsize              | key总数                 |
| expire key seconds  | 设置key过期时间         |
| info memory         | 查询redis的内存占用情况 |
| keys *              | 查看所有keys            |
| object encoding key | 测试编码                |
| ttl key             | 测试key剩余存活时间     |
| type key            | 测试类型                |



---

#### 0x02 string

| Command                                     | Description |
| ------------------------------------------- | ----------- |
| append key | 追加 |
| del key |  |
| exists key |  |
| get key                                     |         |
| getset key value | 取&设 |
| set key val [ex seconds] \[px milliseconds] \[nx\|xx] | 1. ex seconds 是秒级过期时间<br>2. px milliseconds是毫秒级过期时间<br>3. nx是key必须不存在才可以设置成功，用于添加<br>4. xx是key必须存在才可以设置成功，用于更新 |
| setnx key value | 不存在则添加 |
| strlen | string长度 |
|  |  |
| decr key | integer - 1 |
| decrby key delta | integer - delta |
| incr key | integer + 1 |
| incrby key delta | integer + delta |
| incrbyfloat key delta |  |
| mget key [key...] | 同时获取多个key |
| mset key value [key value ...] | 同时设置多个key |
| getrange key 0 0 | 获取一个区间，区间范围左闭右闭 |
| setrange key 0 "value with space" | 从某个offset开始设值，有则覆盖 |



----

#### 0x03 hash




| Command                             | Description                       |
| ----------------------------------- | --------------------------------- |
| hdel key field [field ...]          | 删                                |
| hexists key field                   |                                   |
| hget key field                      |                                   |
| hlen key                            | hashtable大小                     |
| hset key field value                | 增                                |
| hsetnx key field value              | 如果不存在则添加                  |
| strlen key field                    | 计算value的string长度             |
|                                     |                                   |
| hincrby key field                   |                                   |
| hincrbyfloat key field              |                                   |
| hmget key field [field ...]         |                                   |
| hmset key field value [field value] |                                   |
|                                     |                                   |
|                                     |                                   |
| hkeys key                           | all fields                        |
| hvals key                           | all values                        |
| hgetall key                         | key1 val1 key2 val2 key3 val3 ... |



------

#### 0x04 list

​    1. 支持为负数的索引，-1是指倒数第一个元素，这与python类似



##### list应用模式

1. lpush + lpop = stack
2. lpush + rpop = queue
3. lpush + ltrim = capped collection 固定集合
4. lpush + brpop = message queue



| Command                              | Description                                                  |
| ------------------------------------ | ------------------------------------------------------------ |
| blpop key [key ...] timeout          | 1. 如果empty且timeout>0，则等待timeout时间超时返回<br>2. 如果empty且timeout=0，则无限等待<br>3. 如果不empty，则立即返回<br>4. 多个keys时，从左至右遍历keys，一旦有一个键能弹出就立即返回 |
| brpop key [key ...] timeout          |                                                              |
| lindex key -1                        | 取下标-1的元素                                               |
| insert key before\|after pivot value | 在pivot的前面/后面插入value                                  |
| llen key                             | 长度                                                         |
| lpop key                             |                                                              |
| lpush key value [value ...]          |                                                              |
| lrange key 0 -1                      | 打印所有元素                                                 |
| lrem key count value                 | 1. count > 0，从左到右删除最多count个元素<br>2. count < 0，从右到左删除最多count个元素<br>3. count == 0，删除所有 |
| lset key index value                 | 设                                                           |
| ltrim key start end                  | 只保留[start, end]之间的元素                                 |
| rpush key value [ value ...]         |                                                              |
| rpop key                             |                                                              |



----

#### 0x05 set

1. 增删改查，并交补差




| Command                        | Description          |
| ------------------------------ | -------------------- |
| sadd key element [element...]  | 增                   |
| scard key                      | 计算元素个数         |
| sismember key element          | 查                   |
| smembers key                   | 无序返回all elements |
| spop key [count]               | 随机弹出count个元素  |
| srandmember key [count]        | 随机返回count个元素  |
| srem key element [element ...] | 删                   |
|                                |                      |
| sdiff key [key ...]            | 差                   |
| sdiffstore dest key [key ...]  | 存储结果到dest中     |
| sinter key [key ...]           | 交                   |
| sinterstore dest key [key ...] |                      |
| sunion key [key ...]           | 并                   |
| sunionstore dest key [key ...] |                      |



------

#### 0x03 References

1. [Index Merge Optimization](https://dev.mysql.com/doc/refman/8.0/en/index-merge-optimization.html)
2. [Redis 教程](http://www.runoob.com/redis/redis-tutorial.html)