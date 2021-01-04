

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

#  显示所有db
show dbs

# 插入数据
db.movies.insert ({ _id:10, name:"apple"})
db.daily_7d3b4028847acfff.find ({ _id:ObjectId("5fa6c70d8407a870e9fff94f") })

# 查询数据
db.movies.find()

# 清空数据
db.movies.remove({})

```



在secondary机器上执行：

```shell
use local
db.oplog.rs.findOne()

```

