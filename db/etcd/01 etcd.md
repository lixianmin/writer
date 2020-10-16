

-----

#### 0x01 安装

##### 01 使用brew安装

```shell
brew install etcd
brew services start etcd
```



##### 02 docker单机版

```shell
#!/bin/bash
# 参考：https://hub.docker.com/r/bitnami/etcd/

docker run -d --name etcd-server \
    --publish 2379:2379 \
    --publish 2380:2380 \
    --env ETCD_ADVERTISE_CLIENT_URLS=http://etcd-server:2379 \
    quay.io/coreos/etcd:v3.4.13
```



##### 03 docker集群版

```shell
#!/bin/bash
# 参考：https://www.cnblogs.com/itzgr/p/9920897.html

# 使用更新更频繁的bitnami/etcd，无法将volume映射出来，报etcdmain: cannot access data directory错，参考以下链接
# https://stackoverflow.com/questions/24288616/permission-denied-on-accessing-host-directory-in-docker
# 等均不能解决，所以最后使用quay.io上的etcd，这个比较奇怪的地方是lastest版本实际上是2018年的，于是指定使用了v3.4.13版本

# mkdir -p /var/log/etcd/			    #建议创建etcd日志保存目录
# mkdir -p /data/etcd				      #建议创建单独的etcd数据目录
REGISTRY=quay.io/coreos/etcd			#建议使用此仓库
ETCD_VERSION=v3.4.13			        #设置etcd版本
TOKEN=etcd-tour                   #设置唯一集群ID
CLUSTER_STATE=new				          #置为新建集群

#设置三个节点的name
NAME_1=tourwords
NAME_2=tourwords-1-1
NAME_3=tourwords-1-2

HOST_1=192.168.16.10
HOST_2=192.168.16.11
HOST_3=192.168.16.12              #设置三个节点的IP

#设置集群所有节点信息
CLUSTER=${NAME_1}=http://${HOST_1}:2380,${NAME_2}=http://${HOST_2}:2380,${NAME_3}=http://${HOST_3}:2380
DATA_DIR=/data/etcd				        #设置集群etcd数据节点


THIS_NAME=`hostname`
THIS_IP=$(ifconfig eth0 | grep inet | grep -v inet6 | awk '{ print $2}')

echo 'hostname:' $THIS_NAME
echo 'ip:' $THIS_IP

docker run -d \
   -p 2379:2379 \
   -p 2380:2380 \
   --env ALLOW_NONE_AUTHENTICATION=yes \
   --env ETCD_ADVERTISE_CLIENT_URLS=http://${THIS_IP}:2379 \
   --env ETCD_LISTEN_CLIENT_URLS=http://0.0.0.0:2379 \
   --env ETCD_INITIAL_ADVERTISE_PEER_URLS=http://${THIS_IP}:2380 \
   --env ETCD_LISTEN_PEER_URLS=http://0.0.0.0:2380 \
   --env ETCD_INITIAL_CLUSTER=${CLUSTER} \
   --env ETCD_INITIAL_CLUSTER_STATE=${CLUSTER_STATE} \
   --env ETCD_INITIAL_CLUSTER_TOKEN=${TOKEN} \
   --env ETCD_NAME=${THIS_NAME} \
   --env ETCD_DATA_DIR=/data \
   --volume=${DATA_DIR}:/data \
   --name etcd ${REGISTRY}:${ETCD_VERSION}
```





-----

#### 0x02 基本命令

```shell
# 添加root用户，会提示输入密码，示例使用123456
# root用户默认拥有root权限
etcdctl user add root

# 开启验证
etcdctl --user="root" --password="123456" auth enable
```



-----

#### 0x09 References

1. https://github.com/etcd-io/etcd
2. 

