

安装：

```shell
# 安装supervisor
brew install supervisor

# mac supervisor 解决 http://localhost:9001 refused connection
sudo ln -sv /usr/local/etc/supervisord.ini /etc/supervisord.conf


```



修改配置文件，vim /etc/supervisord.conf ，找到如下部分： 把前面的注释去掉

```ini
[inet_http_server]        ; inet (TCP) server disabled by default
port=127.0.0.1:9001       ; ip_address:port specifier, *:port for all iface
username=user            ; default is no username (open server)
password=123             ; default is no password (open server)
```



```shell
# 重启supervisor
brew services restart supervisor

supervisorctl
# 进入控制台后，运行help, status等指令检查是否正确启动
```



1. 将*.ini配置文件放到/usr/local/etc/supervisor.d目录下面（阿里云在 /etc/supervisord.d 下面）
2. btc-trade.ini这样的文件名是没有问题的，但是在ini文件内部，只能使用[program:btctrade]这样的名字



```ini
; 不能使用btc-trade这样的名字
[program:btctrade]

; 不支持 ~/go/src/....这样的路径
command         = /Users/xmli/go/src/github.com/lixianmin/btc-trade/btc-trade
directory       = /Users/xmli/go/src/github.com/lixianmin/btc-trade
stdout_logfile  = /Users/xmli/log/btc-trade_stdout.log
stderr_logfile  = /Users/xmli/log/btc-trade_stderr.log

stdout_logfile_maxbytes=10MB
stderr_logfile_maxbytes=10MB
autostart=true
autorestart=true
```



运行的时候如果报：[unix:///var/run/supervisor.sock no such file](https://superuser.com/questions/1069276/unix-var-run-supervisor-sock-no-such-file)，尝试先运行supervisord，然后再运行supervisorctl



---

#### References

1. **http://supervisord.org/**
2. [mac 安装和使用 supervisor --过程](http://blog.sina.com.cn/s/blog_ac47d6b30102xsfm.html)