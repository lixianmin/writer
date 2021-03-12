

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





```


rpm -ivh git19-scldevel-1.2-4.el6.x86_64.rpm


cd /usr/share/go-server/bin
cd /usr/share/go-agent/bin
cd /var/log/go-server


```



