

1. collection的概念，来源于编程语言中的collection，常见的就是hash table, map等，对应到数据库中自然就是table了
2. document的概念，来源于我们需要随时修改document中的一些字段，对应到json



-----

#### 02 shell

1. [在线文档](https://docs.mongodb.com/manual/reference/method/db.collection.find/)



```bash
# 通过以下命令进入shell
mongo

# 打印help
help

#  显示所有db的名称和大小
show dbs

# 插入数据
db.movies.insert ({ _id:10, name:"apple"})
db.daily_7d3b4028847acfff.find ({ _id:ObjectId("5fa6c70d8407a870e9fff94f") })

# 查询数据
db.movies.find()

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



--------

#### 09 References

1. [MongoDB的复制集](https://www.bookstack.cn/read/linfenliang-mongodb/chapter7.md)
2. 