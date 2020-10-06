



#### 0x01 常用命令

1. yum install nginx
2. vim /etc/nginx/nginx.conf 编辑conf文件



|                 |                                             |      |
| --------------- | ------------------------------------------- | ---- |
| nginx           | 打开nginx                                   |      |
| nginx -h        | 帮助信息                                    |      |
| nginx -s quit   | 优雅的停止nginx服务（等待处理完所有的请求） |      |
| nginx -s reload | 重启加载配置，然后以优雅的方式重启nginx     |      |
| nginx -s reopen | 重启nginx                                   |      |
| nginx -s stop   | 强制停止ngin服务                            |      |
| nginx -t        | test配置文件是否有语法错误                  |      |
| nginx -v        | 显示版本信息并退出                          |      |



#### 0x02 设置代理

```nginx
stream {
        upstream shadowsocks {
                server 127.0.0.1:7878;  # 原shadowsocks开在7878
        }

        server {
                listen 9090;						# 新的端口开在9090
                listen 9090 udp;
                proxy_pass shadowsocks;
        }
}
```



#### 0x03 重定向到443

1. 去[百度云](https://cloud.baidu.com/product/ssl.html)平台购买证书
2. 创建实例的时候，选择 TrustAsia --> 域名型DV --> 单域名版，这样的证书不要钱
3. 



