



1. 使用brew install supervisor没有搞定
2. 使用pip install supervisor --user 安装成功
3. 将*.ini配置文件放到/usr/local/etc/supervisor.d目录下面
4. btc-trade.ini这样的文件名是没有问题的，但是在ini文件内部，只能使用[program:btctrade]这样的名字



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



mac supervisor 解决 http://localhost:9001 refused connection

 ```shell
 sudo ln -sv /usr/local/etc/supervisord.ini /etc/supervisord.conf
 ```



---

#### References

1. **http://supervisord.org/**
2. 