

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
# 参考：https://www.artacode.com/posts/etcd/install/

docker run -d -v /usr/share/ca-certificates/:/etc/ssl/certs -p 4001:4001 -p 2380:2380 -p 2379:2379 \
 --name etcd quay.io/coreos/etcd:latest /usr/local/bin/etcd \
 -name etcd0 \
 -advertise-client-urls http://127.0.0.1:2379,http://127.0.0.1:4001 \
 -listen-client-urls http://0.0.0.0:2379,http://0.0.0.0:4001 \
 -initial-advertise-peer-urls http://127.0.0.1:2380 \
 -listen-peer-urls http://0.0.0.0:2380 \
 -initial-cluster-token etcd-cluster-1 \
 -initial-cluster etcd0=http://127.0.0.1:2380 \
 -initial-cluster-state new
```



##### 03 docker集群版

```shell
#!/bin/bash
# 参考：https://www.cnblogs.com/itzgr/p/9920897.html

# mkdir -p /var/log/etcd/			    #建议创建etcd日志保存目录
# mkdir -p /data/etcd				      #建议创建单独的etcd数据目录
REGISTRY=quay.io/coreos/etcd			#建议使用此仓库
ETCD_VERSION=latest				        #设置etcd版本
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
   --volume=${DATA_DIR}:/etcd-data \
   --name etcd ${REGISTRY}:${ETCD_VERSION} \
   /usr/local/bin/etcd \
   --data-dir=${DATA_DIR} --name ${THIS_NAME} \
   --initial-advertise-peer-urls http://${THIS_IP}:2380 --listen-peer-urls http://0.0.0.0:2380 \
   --advertise-client-urls http://${THIS_IP}:2379 --listen-client-urls http://0.0.0.0:2379 \
   --initial-cluster ${CLUSTER} \
   --initial-cluster-state ${CLUSTER_STATE} --initial-cluster-token ${TOKEN}
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

