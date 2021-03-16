#### 1 安装

```shell
# 1. 下载
https://www.gocd.org/download

# 所有的修改和启动都需要sudo权限，所以可以直接sudo su
# 2. 安装server
sudo rpm -ivh go-server-${version}.noarch.rpm
cd /usr/share/go-server/bin
sudo start go-server

# 3. 安装agent
sudo rpm -ivh go-agent-${version}.noarch.rpm
cd  /usr/share/go-agent/bin
sudo start go-agent

# 浏览器打开
http://localhost:8153
```



#### 2 重新安装

```shell
rm -rf /var/log/go-server
rm -rf /var/log/go-agent
rm -rf /var/lib/go-server
rm -rf /var/lib/go-agent

cd /home/batsdk/software/gocd
rpm -e go-server-21.1.0-12439.noarch
rpm -e go-agent-21.1.0-12439.noarch
rpm -ivh go-server-21.1.0-12439.noarch.rpm
rpm -ivh go-agent-21.1.0-12439.noarch.rpm 

```



#### 3 配置

1. 为了让gocd能正常打包，采用了很多办法，比如使用高权限的root用户，使用配置环境完善的batsdk用户，最终都很难运行起来。
2. 现在的方案是：使用go用户，并将go用户的打包环境配置好。



```shell
# gocd默认使用go用户运行程序
# 修改go用户的密码
passwd go

# 登录到go用户下，安装deck
deck install devel
deck install go-1.15.2

# 使用batsdk账号，在/usr/bin下面建立git的链接，否则gocd找不到。不能建到/usr/local/bin下面
sudo ln -s /var/go/.deck/1.0/devel/1.0/bin/git /usr/bin/git

# 自己建一个code目录，将代码下载下来，测试一下流程能否跑通，可能需要调整.ssh
```



其它没起作用的操作：

```shell

# go-server与go-agent文件的140行，如果要修改执行的用户，则需要对以下文件有权限
/var/lib/go-agent/run/go-agent.pid
/var/lib/go-server/run/go-server.pid
/var/lib/go-agent/wrapper.log
/var/lib/go-server/wrapper.log
```



