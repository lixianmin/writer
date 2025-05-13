

------

#### 01 命令行

1. redis-cli -h  127.0.0.1.	  客户端
2. 很多方法都带一个key参数，这个应该理解为OO中的this
3. range类操作在redis中的区间都是**左闭右闭**的



```shell

docker run --name redis_alpine -p 6379:6379 --restart unless-stopped -d redis:alpine

```





| Command                              | Description                  |
| ------------------------------------ | ---------------------------- |
| dbsize                               | key总数                      |
| expire key seconds                   | 设置key过期时间              |
| info memory                          | 查询redis的内存占用情况      |
| info server                          | 版本号，端口，conf路径等信息 |
| keys *                               | 查看所有keys                 |
| object encoding key                  | 测试编码                     |
| redis-cli shutdown                   | 关闭redis服务器              |
| redis-server ~/.jumbo/etc/redis.conf | 启动redis服务器              |
| ttl key                              | 测试key剩余存活时间          |
| type key                             | 测试类型                     |



---

#### 02 string

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

#### 03 hash




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

#### [04 list](http://redisdoc.com/list/index.html)

​    1. 支持为负数的索引，-1是指倒数第一个元素，这与python类似



##### 01 list应用模式

1. lpush + lpop = stack
2. lpush + rpop = queue
3. lpush + ltrim = capped collection 固定集合
4. lpush + brpop = message queue



| Command                                                      | Notes                                                        |           |
| ------------------------------------------------------------ | ------------------------------------------------------------ | --------- |
| blpop key [key ...] timeout                                  | 1. 如果empty且timeout>0，则等待timeout时间超时返回<br>2. 如果empty且timeout=0，则无限等待<br>3. 如果不empty，则立即返回<br>4. 多个keys时，从左至右遍历keys，一旦有一个键能弹出就立即返回 |           |
| brpop key [key ...] timeout                                  |                                                              |           |
| lindex key -1                                                | 取下标-1的元素                                               |           |
| insert key before\|after pivot value                         | 在pivot的前面/后面插入value                                  |           |
| llen key                                                     | 长度                                                         |           |
| lpop key                                                     |                                                              |           |
| lpush key value [value ...]                                  |                                                              |           |
| [lrange](http://redisdoc.com/list/lrange.html) key start stop | 1. lrange key 0 -1   返回所有元素<br />2. lrange key 0 10  返回11个元素，区间[start, stop] | O(S+N)    |
| lrem key count value                                         | 1. count > 0，从左到右删除最多count个元素<br>2. count < 0，从右到左删除最多count个元素<br>3. count == 0，删除所有 |           |
| [lset](http://redisdoc.com/list/lset.html) key index value   | 1. key[index]=value<br />2. 设置头尾元素的复杂度为 O(1)，其它为O(N) | O(1)/O(N) |
| ltrim key start end                                          | 只保留[start, end]之间的元素                                 |           |
| rpush key value [ value ...]                                 |                                                              |           |
| rpop key                                                     |                                                              |           |



----

#### 05 set

1. 增删改查，并交补差




| Command                        | Notes                |
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



---

#### [06 sorted set](http://redisdoc.com/sorted_set/index.html)

1. 有库集合的成员是唯一的，但score (**double类型**) 可以重复
2. rank指排名，或下标：[0, n-1]
3. score指分数，元素按分数从小到大排序



| Command                                                      | Notes                                                        |             |
| ------------------------------------------------------------ | ------------------------------------------------------------ | ----------- |
| [zadd](http://redisdoc.com/sorted_set/zadd.html) key score1 member1 [score2 member2] | 添加成员，或更新成员分数                                     |             |
| [zcard](http://www.redis.cn/commands/zcard.html) key         | 获取集合（容器）元素个数                                     | O(1)        |
| [zscore](http://redisdoc.com/sorted_set/zscore.html) key member | 返回member的score值                                          |             |
| zinterstore dest N key1 key2 keyN                            | 计算交集，存储到dest集合中                                   |             |
| [zrange](http://redisdoc.com/sorted_set/zrange.html) key start stop [withscores] | 1. [start, stop]，-1代表下标上界  <br />2. withscores会同时返回score | O(log(N)+M) |
| zrevrange key start stop [withscores]                        | 按score由大到小的序列返回，socre相同的成员按字典逆序排列     |             |
| zRemRangeByRank key start stop                               | 移除下标在[start, stop]的成员                                |             |
| zRemRangeByScore key min max                                 | 移除score在[min, max]的成员                                  |             |
|                                                              |                                                              |             |



-----

#### [07 HyperLogLog](http://redisdoc.com/hyperloglog/index.html)



| Command                                                     | Notes                                   |      |
| ----------------------------------------------------------- | --------------------------------------- | ---- |
| pfadd key element [element...]                              | 添加任意数量的元素到指定的HyperLogLog中 | O(1) |
| [pfcount](http://redisdoc.com/hyperloglog/pfcount.html) key | 返回key指定的HyperLogLog的近似基数      | O(1) |
|                                                             |                                         |      |





------

#### 09 References

1. [Redis 命令参考](http://redisdoc.com)
2. [Index Merge Optimization](https://dev.mysql.com/doc/refman/8.0/en/index-merge-optimization.html)
3. [Redis 教程](http://www.runoob.com/redis/redis-tutorial.html)