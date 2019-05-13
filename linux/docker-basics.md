



---

#### 安装并启动

1. brew cask install docker，500M，直接下载：https://download.docker.com/mac/stable/Docker.dmg
2. 在  Perferences... -> Daemon -> Registry mirrors中添加镜像https://registry.docker-cn.com， 并重启





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





----
#### 常用命令



|    命令                              |    描述           |
| -------------------------------- | ------------- |
| docker build -t exchange-ws.image . | build image   |
| docker run -p 2745:2745 exchange-ws.bin | 运行image     |
|                                  |               |
| docker images                    | 显示所有image |
| docker rmi image-id              | 删除image     |
|                                  |               |
| docker ps -a                     | 显示所有进程  |
| docker rm [container-id]         | 删除容器      |
| docker rm  -f [container-id] | 强制删除多出来的容器 |
| docker restart [container-id] | 重启容器 |
| docker stop [container-id] | 停止容器运行 |
| docker exec -it [container-id] bash | 进入到容器的shell，进一步查看 |



| 命令                               | 描述                    |
| ---------------------------------- | ----------------------- |
| docker-compose up -d               | 启动docker              |
| docker-comopose start/stop/restart | 启动/停止/重启container |
| docker-compose logs -f             | 打印日志                |
|                                    |                         |
|                                    |                         |





1. 删除kline2的那些没有用的images

   ```shell
   docker images  | grep kline2 | awk '{ if($2 == "<none>") system("docker rmi "$3) }'
   ```

2.



---

#### references

1. [macOS 安装 Docker](https://yeasy.gitbooks.io/docker_practice/install/mac.html)
2. [如何使用Docker部署Go Web应用程序](http://www.infoq.com/cn/articles/how-to-deploy-a-go-web-application-with-docker)
3. [Gin实践 连载九 将Golang应用部署到Docker](https://segmentfault.com/a/1190000013960558)