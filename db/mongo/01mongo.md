

1. collection的概念，来源于编程语言中的collection，常见的就是hash table, map等，对应到数据库中自然就是table了
2. document的概念，来源于我们需要随时修改document中的一些字段，对应到json



-----

#### 02 shell

1. [在线文档](https://docs.mongodb.com/manual/reference/method/db.collection.find/)



##### 01 basics

```bash
# 通过以下命令进入shell
mongo

# 打印help
help

#  显示所有db的名称和大小
show dbs

# 清空数据
db.movies.remove({})

db.printReplicationInfo()				# oplog状态
db.printSlaveReplicationInfo()	# 从节点信息

# 1. 从节点默认不可读，需要在『从节点』执行如下操作激活可读
# 2. 但需要每链接都执行一遍db
# 3. 仍然不能执行 show dbs; show collections这样的操作
rs.slaveOk()  # 设置从节点可读，或db.setSlaveOk()，或db.getMongo().setSlaveOk()

# rs是replication set的缩写
rs.conf()			# 复制集配置
rs.isMaster()	# 是否是主节点，集群信息
rs.status()		# 复制集状态
```



在secondary机器上执行：

```shell
use local
db.oplog.rs.findOne()
```



##### 02 查询

```bash
use crash_records

# 统计个数
db.crash_records.count()

# 查询所有
db.crash_records.find()

# 只返回部分字段，其中第一个参数为查询条件，为{}代表查询所有
db.crash_records.find({}, {appKey:1, serverTime:1})

# 只是某些字段不需要，其它的全返回
db.crash_records.find({}, {errorTrace:0})

```



##### 03 索引



```bash
# 在后台创建serverTime的升序索引
db.crash_records.createIndex({serverTime:1}, {background:true})

# 查看所有索引
db.crash_records.getIndexes()

# explain
db.crash_records.find({age: 18}).explain()
```



##### 04 插入

```bash
# 插入数据
db.movies.insert ({ _id:10, name:"apple"})
db.daily_7d3b4028847acfff.find ({ _id:ObjectId("5fa6c70d8407a870e9fff94f") })

# 将数据从一个db中select出来，然后插入到另一个db中
use src
var docs = db.crash_records.find()	# 这里返回的iterator，如果想反复使用，需要使用.toArray()

use dest
docs.forEach(function(x) { db.crash_records.insert(x); } )	# 如果想执行upsert，则需要使用save()方法
```



##### 05 打印count()数最大的10张表



```js
db.getCollectionNames().map(function(x){return db[x]}).sort(function(a,b){ return a.count() - b.count() }).slice(-10).forEach(function(x){ print(x, x.count())})

```



##### 06 compact磁盘

```js

// 如果是primary节点，先强制变成sencodary节点
rs.stepdown(120)；

# 整理磁盘，有可能卡死进程
db.runCommand({compact:"collection", force:<boolen>})
db.currentOp()    # 查询opid
db.killOp(<opid>)	# 按opid杀死操作进程

```





--------

#### 09 References

1. [MongoDB的复制集](https://www.bookstack.cn/read/linfenliang-mongodb/chapter7.md)
2. [MongoDB中ObjectId的误区，以及引起的一系列问题](https://blog.csdn.net/xiamizy/article/details/41521025)
3. 