

--------------

#### 1 新流程



1. 如果修改了$PATH环境变量, 如果想让进程加载新的环境变量, 需要kill掉supervisord进程



```shell
# 安装

# 生成配置文件
cd
mkdir -p etc/supervisor
cd etc/supervisor
echo_supervisord_conf > supervisord.conf

# 修改包含的配置文件
vim supervisord.conf  # 然后G到最文件尾部

# 移除最后【两】行的注释，然后修改为：
[include]
files = conf/*.ini

# 创建conf目录，然后在conf中加入.ini的配置文件
mkdir conf

# 启动supervisor.d
supervisord -c supervisord.conf 
supervisorctl -c supervisord.conf  # 必须使用同一个conf文件，否则会找不到
```



在 conf目录中加入btc-trade.ini文件

```ini
; 字段中可以带 -
[program:btc-trade]

; 不支持 ~/go/src/....这样的路径
command         = /Users/xmli/go/src/github.com/lixianmin/btc-trade/btc-trade
directory       = /Users/xmli/go/src/github.com/lixianmin/btc-trade
; stdout_logfile  = /Users/xmli/log/btc-trade_stdout.log
; stderr_logfile  = /Users/xmli/log/btc-trade_stderr.log

; stdout_logfile_maxbytes=10MB
; stderr_logfile_maxbytes=10MB
autostart=true
autorestart=true
```



---

#### 2  supervisorctl



```shell
# 进入控制台后，运行help, status等指令检查是否正确启动
supervisorctl

# 重新加载配置，如果有新的ini文件，会加入
update
```



---------------------------



安装：

```shell
# 安装supervisor
brew install supervisor

# mac supervisor 解决 http://localhost:9001 refused connection
sudo ln -sv /usr/local/etc/supervisord.ini /etc/supervisord.conf


```



```shell
# 重启supervisor
brew services restart supervisor


```



1. 将*.ini配置文件放到/usr/local/etc/supervisor.d目录下面（阿里云在 /etc/supervisord.d 下面）
2. 运行的时候如果报：[unix:///var/run/supervisor.sock no such file](https://superuser.com/questions/1069276/unix-var-run-supervisor-sock-no-such-file)，尝试先运行supervisord，然后再运行supervisorctl



---

#### References

1. **http://supervisord.org/**
2. [mac 安装和使用 supervisor --过程](http://blog.sina.com.cn/s/blog_ac47d6b30102xsfm.html)
3. [使用 supervisor 管理进程](https://liyangliang.me/posts/2015/06/using-supervisor)