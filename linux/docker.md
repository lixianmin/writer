
---

#### 01 Basics

##### 1 macos安装

```shell
# brew install docker, 不要安装这个, 这个不能用, 必须安装桌面版的, 虽然大了10倍不止
brew install --cask docker

# 安装后启动, 然后会自己安装docker与docker-compose命令到命令行
open /Applications/Docker.app

# 执行 docker ps
# 如果报: -bash: /opt/homebrew/bin/docker: No such file or directory
# 则执行ln命令创建新的链接
docker ps
ln -s /usr/local/bin/docker /opt/homebrew/bin/docker
```



```bash
# 这句会下载一个完成的golang编译环境，这些实际上在运行时并不需要
# FROM golang:1.11

# ubuntu环境，84.1M，但不带vi
# FROM ubuntu

# busybox环境，1.16M，带vi
FROM busybox

# Expose the application on port 2745
EXPOSE 2745

WORKDIR $GOPATH/src/coinbene.com/exchange-ws-golang
COPY  *.bin $GOPATH/src/coinbene.com/exchange-ws-golang
COPY  res   $GOPATH/src/coinbene.com/exchange-ws-golang/res

ENTRYPOINT ["./exchange-ws-golang.bin"]
```



##### 2 centos

1. 参考文档： https://docs.docker.com/engine/install/centos/

```shell

sudo yum install -y yum-utils
sudo yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo

sudo yum install docker-ce docker-ce-cli containerd.io

# 安装过程中会报错：package docker-ce-3:19.03.10-3.el7.x86_64 requires containerd.io >= 1.2.2-3, but none of the providers can be installe

yum install -y https://download.docker.com/linux/centos/7/x86_64/stable/Packages/containerd.io-1.2.6-3.3.el7.x86_64.rpm

# 然后再执行：
sudo yum install docker-ce docker-ce-cli containerd.io

# 启动docker
sudo systemctl start docker
sudo systemctl enable docker

# 去这个位置查docker-compose的最新版本 https://github.com/docker/compose/releases
sudo curl -L "https://github.com/docker/compose/releases/download/v2.12.2/docker-compose-$(uname -s | tr A-Z a-z)-$(uname -m)" -o /usr/local/bin/docker-compose


# 解决 Got permission denied while trying to connect to the Docker daemon socket at unix:///var/run/docker.sock
sudo chmod 666 /var/run/docker.sock
```



#### 02 常用命令



##### docker basics

|    命令                              |    描述           |
| -------------------------------- | ------------- |
| docker build -t lixianmin/golang-mini:latest . | build image，最后的.指Dockerfile的位置 |
| docker push lixianmin/golang-mini:latest | 把build完成的image推到docker hub上去 |
|  |  |
| docker info | 打印docker信息 |
| docker run -p 2745:2745 exchange-ws.bin | 运行image     |
| docker logs [container-id] | 如果启动失败了，通过logs命令查看失败原因 |
|                                  |               |
| docker ps -a                     | 显示所有进程  |
| docker rm [container-id]         | 删除容器      |
| docker rm  -f [container-id] | 强制删除多出来的容器 |
| docker restart [container-id] | 重启容器 |
| docker search logstash | 搜索名字带logstash的容器 |
| docker stop [container-id] | 停止容器运行 |
| docker exec -it [container-id] bash | 进入到容器的shell，进一步查看 |



##### docker-compose

```shell
# 编译
docker-compose build

# 启动docker
docker-compose up -d

# 启动/停止/重启container
docker-comopose start/ddf/restart

# 停止
docker-compose down

# 打印日志
docker-compose logs -f
```



##### docker images

```bash
# 删除指定id的image
docker rmi image-id

# 删除kline2的那些没有用的images
# docker images  | grep kline2 | awk '{ if($2 == "<none>") system("docker rmi "$3) }'
docker image prune
```



##### docker network

```shell
# 测试hadoop-net这个网络是否存在，如果不存在则创建之 
if ! docker network inspect hadoop-net &>/dev/null
then
  # --driver overly 创建一个overlay网络，这样可以形成一个跨主机网络群组，命名为hadoop-net
  # --driver bridge 创建一个单机网络，只有自己可以访问
  docker network create --driver overlay hadoop-net || error_exit "初始化 overlay 网络失败"
fi
```



##### docker service

```shell
# 列出service的id和name
docker service ls

# 热更新docker的image，其中hadoop-node21是服务name
docker service update --image yuanxulei/hadoop:2.6.0-cdh5.15.0  hadoop-node021

# 强制重启service，使用服务的id或name都可以
docker service update --force p52aylu6i0tx

# 列出service
# --filter "name=xxx"，按名称过滤
# --format 使用Golang模板打印输出，下例只会输出name列
if [ -z $(docker service ls --filter "name=$name"  --format "{{.Name}}") ]
then
    echo "开始创建第 $i 个节点(共 $nodeNum 个) $name ..."
    docker service create \
      --name $name \
      --hostname $name \
      --constraint node.hostname==$hostname \
      --network hadoop-net \
      --replicas 1 \
      --endpoint-mode dnsrr \
      # type=bind，意味着绑定一个已存在的目录/文件（由src指定）到容器中
      --mount type=bind,src=/etc/localtime,dst=/etc/localtime \
      --mount type=bind,src=/data/config/hadoop/,dst=/config/hadoop \
      --mount type=bind,src=/data/hadoop/hdfs/,dst=/tmp/hadoop-root \
      --mount type=bind,src=/data/hadoop/logs/,dst=/usr/local/hadoop/logs \
      --mount type=bind,src=/data/hadoop/submit/,dst=/submit \
      yuanxulei/hadoop:2.6.0-cdh5.15.0 || error_exit "创建 $name 失败"
    echo "创建 $name 成功"
fi
```



##### docker swarm

```bash
# 打印帮助信息
docker swarm help

# 在0.0.0.0:2377端口监听
docker swarm init --listen-addr 0.0.0.0 > /dev/null

# 1. 生成join-token到文件中，如果出错，则将错误发送到/dev/null
# 2. manger代表是主节点，worker代表是从节点， -q代表只输出token不打印其它信息
# 3. 当if后接shell命令时，由于返回0代表着程序正常退出，因此0判定为true，其它1/2..判定为false
# 4. 在其它情况下，if [n]; then...if，无论n=0/1/2..都返回true
if docker swarm join-token manager -q > ../config/swarm_token 2> /dev/null
then
	echo 1
fi

# 打印docker的manager状态，其中leader是主节点，reachable是可达的manger节点，显示为空的是worker节点
docker node ls
docker node promote hostname	# 升级为manager节点
docker node demote hostname 	# 降级为worker节点
```



-----

#### 03 时区

1.  到目前为止仍然没有搞清楚时区的规则
2. `--env TZ=Asia/Shanghai`，在sqoop把时间信息从mysql(datetime)导出到hive(bigint)时，发生了-8小时的问题，在某个docker中加入这个好使了；但是加入这个之后，发现某些docker中执行date返回的时间信息错了，不知道什么原因
3. `--mount type=bind,src=/etc/localtime,dst=/etc/localtime ` 这个应该可以把service中的date时间映射到东8区，但是加入TZ环境变量后消息了，原因不详细



-----

#### 04 docker-compose.yml



```yml
version: "3.8"
services:
  golang:
    build:
      context: .
      dockerfile: Dockerfile
    restart: always
    network_mode: "host"

    # 1. 如果是host模式，其实不用 ports 映射端口，但是不知道为什么，在macos上host模式就是无法映射出端口。在centos上面是成功的。
    # 2. 但是，如果不使用host模式，那么钉钉消息就发送不出来，使用curl测试也发送不了数据，所以还是使用host模式吧
    ports:  # 宿主机端口:容器端口
      - 8441:8441
      - 8442:8442
      - 8443:8443

    volumes:
      # 第一个目录是docker外的主机目录，第二个目录是docker内的目录，相互影射
      - /data/logs/tour-words:/app/logs:rw
    environment:
      ENV: ${ENV:-DEV}
```



---

#### 09 references

1. [macOS 安装 Docker](https://yeasy.gitbooks.io/docker_practice/install/mac.html)
2. [如何使用Docker部署Go Web应用程序](http://www.infoq.com/cn/articles/how-to-deploy-a-go-web-application-with-docker)
3. [Gin实践 连载九 将Golang应用部署到Docker](https://segmentfault.com/a/1190000013960558)