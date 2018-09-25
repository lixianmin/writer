



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



|    命令                              |    描述           |      |
| -------------------------------- | ------------- | ---- |
| docker build -t ws-image .       | build image   |      |
| docker run -p 2745:2745 ws-image | 运行image     |      |
|                                  |               |      |
| docker images                    | 显示所有image |      |
| docker rmi image-id              | 删除image     |      |
|                                  |               |      |
| docker ps -a                     | 显示所有进程  |      |
| docker rm [container-id]         | 删除容器      |      |
| docker exec -it [container-id] bash | 进入到容器的shell，进一步查看 |      |
|                                  |               |      |



---

#### references

1. [macOS 安装 Docker](https://yeasy.gitbooks.io/docker_practice/install/mac.html)
2. [如何使用Docker部署Go Web应用程序](http://www.infoq.com/cn/articles/how-to-deploy-a-go-web-application-with-docker)
3. [Gin实践 连载九 将Golang应用部署到Docker](https://segmentfault.com/a/1190000013960558)