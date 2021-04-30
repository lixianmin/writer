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


# 创建任务时，pipeline的名字就叫原始项目的名字就好，不要带-pipeline的结尾，因为这其实就是目录的名字，这个目录名会用作进程名
# 在task中加入两个command
./build.nake.sh         # 用于构建
./deploy.gocd.sh {IP}   # 用于发版
```



##### 1 build.naked.sh

```shell
#!/bin/bash

APP_NAME=${PWD##*/}
PID=`ps aux | grep ${APP_NAME} | grep -v grep | awk '{ print $2}'`
if [ "$PID" != "" ] ;then
  kill -9 $PID
  echo "kill old process with name="$APP_NAME", pid="$PID
else
  echo "can not find old process with name="$APP_NAME
fi

IMPORT_PATH=github.com/lixianmin/gonsole
FLAGS="-X $IMPORT_PATH.GitBranchName=`git rev-parse --abbrev-ref HEAD` -X $IMPORT_PATH.GitCommitId=`git log --pretty=format:\"%h\" -1` -X $IMPORT_PATH.AppBuildTime=`date +%Y-%m-%dT%H:%M:%S`"
go build -ldflags "$FLAGS" -mod vendor -gcflags "-N -l"
```



##### 2 deploy.gocd.sh

```shell
#!/bin/bash

# 假设：APP_NAME就是进程所在目录名
# 假设：目标机安装了supervisor
APP_NAME=$(basename `pwd`)
USER_NAME=hello

REMOTE=$USER_NAME@$1
CONFIG_DIR=/home/$USER_NAME/etc/supervisor.d
CONFIG_FILE=$CONFIG_DIR/supervisord.conf

##############################################################
echo 1. ssh $REMOTE "supervisorctl -c $CONFIG_FILE stop $APP_NAME"
ssh $REMOTE "supervisorctl -c $CONFIG_FILE stop $APP_NAME"

##############################################################
echo 2. 创建 $APP_NAME.ini 文件

echo "[program:$APP_NAME]" > $APP_NAME.ini
echo "command     = /home/$USER_NAME/gocd/$APP_NAME/$APP_NAME" >> $APP_NAME.ini
echo "directory   = /home/$USER_NAME/gocd/$APP_NAME" >> $APP_NAME.ini
echo "" >> $APP_NAME.ini
echo "autostart   = true" >> $APP_NAME.ini
echo "autorestart = true" >> $APP_NAME.ini

scp $APP_NAME.ini $REMOTE:$CONFIG_DIR/conf

##############################################################
echo
echo 3. 创建目录并将资源传输到 $APP_NAME
DEST_DIR=/home/$USER_NAME/gocd/$APP_NAME/
ssh $REMOTE "mkdir -p $DEST_DIR"
scp $APP_NAME $REMOTE:$DEST_DIR
scp -r res $REMOTE:$DEST_DIR

GONSOLE_DIR=vendor/github.com/lixianmin/gonsole
ssh $REMOTE "mkdir -p $DEST_DIR/$GONSOLE_DIR"
scp $GONSOLE_DIR/console.html $REMOTE:$DEST_DIR/$GONSOLE_DIR
scp -r $GONSOLE_DIR/res $REMOTE:$DEST_DIR/$GONSOLE_DIR

##############################################################
echo
echo 4. ssh $REMOTE "supervisorctl -c $CONFIG_FILE start $APP_NAME"
ssh $REMOTE "supervisorctl -c $CONFIG_FILE update $APP_NAME"
ssh $REMOTE "supervisorctl -c $CONFIG_FILE start $APP_NAME"
```











---------------------



其它没起作用的操作：

```shell

# go-server与go-agent文件的140行，如果要修改执行的用户，则需要对以下文件有权限
/var/lib/go-agent/run/go-agent.pid
/var/lib/go-server/run/go-server.pid
/var/lib/go-agent/wrapper.log
/var/lib/go-server/wrapper.log
```



