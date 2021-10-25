

-----

#### 



##### 01 docker配置副本集



```shell

# 1. 启动mongo
docker run -d --rm --name mongo-master -p 27017:27017 mongo:3.0.3 mongod --dbpath /data/db  --replSet mongoreplset --oplogSize 128

docker run -d --rm --name mongo-slave -p 27018:27017 mongo:3.0.3 mongod --dbpath /data/db --replSet mongoreplset --oplogSize 128

docker run -d --rm  --name mongo-arbiter -p 27019:27017 mongo:3.0.3 mongod --dbpath /data/db --replSet mongoreplset --smallfiles --oplogSize 128

# 2.集群配置
# 因为macbook本机的ip经常变，测试时需要将下面命令中的ip改一下
docker exec -it mongo-master mongo

config = {_id:"mongoreplset", version:1, members:[{_id:0, host:"172.24.202.186:27017", priority:5}, {_id:1, host:"172.24.202.186:27018", priority:2}, {_id:2, host:"172.24.202.186:27019", priority:3}]}

rs.initiate(config)

# 3.添加用户
use admin 
db.createUser({ user: 'admin', pwd: 'admin', roles: [ { role: "userAdminAnyDatabase", db: "admin" } ] });

# 4.重启mongo
# 先把所有的mongo服务的干掉，不要删除文件再运行下边的脚本
docker run -d --rm --name mongo-master  -v /data/mongo/db/:/data/db -v /data/mongo/configdb/:/data/configdb -p 27017:27017 mongo:latest mongod --dbpath /data/db  --replSet mongoreplset --oplogSize 128 --auth --keyFile=/data/configdb/key_file

docker run -d --rm --name mongo-salve  -v /data/mongo-salve/db/:/data/db -v /data/mongo-salve/configdb/:/data/configdb -p 27017:27017 mongo:latest mongod --dbpath /data/db --replSet mongoreplset --oplogSize 128 --auth --keyFile=/data/configdb/key_file

docker run -d --rm --name mongo-arbiter   -v /data/mongo-arbiter/db/:/data/db -v /data/mongo-arbiter/configdb/:/data/configdb -p 27018:27017 mongo:latest mongod --dbpath /data/db --replSet mongoreplset --smallfiles --oplogSize 128 --auth --keyFile=/data/configdb/key_file
注意，如果想看到日志，可以把-d去掉。

```





##### 02 解决replica sets没有primary节点的问题





```javascript
// 没有primary节点了，需要重新配置一下
cfg = rs.conf();
cfg.members.splice(1,2); // remove 2 servers starting at index 1 - documentation here
rs.reconfig(cfg, {force: true});

```





-----

#### 09 参考文献

1. [docker 下部署mongodb Replica Set 集群](https://www.jianshu.com/p/c3811263fd3a)
2. 