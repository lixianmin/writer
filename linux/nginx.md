



#### 1 常用命令

1. yum install nginx
2. vim /etc/nginx/nginx.conf 编辑conf文件
2. python -m http.server 8000 在python3.x下启动一个测试用的http服务器



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



#### 2 docker搭建nginx

```shell
docker pull nginx
docker run --name nginx -p 80:80 -d nginx

# 创建一些本机的配置目录
mkdir -p /home/ubuntu/etc/nginx
mkdir -p /home/ubuntu/etc/nginx/conf
mkdir -p /home/ubuntu/etc/nginx/logs
mkdir -p /home/ubuntu/data/nginx

# 将容器中的相应文件copy到刚创建的管理目录中
# 注：docker cp 67e 中的 "67e" 为容器ID前缀，只要唯一就好了
docker cp 67e:/etc/nginx/nginx.conf /home/ubuntu/etc/nginx/
docker cp 67e:/etc/nginx/conf.d     /home/ubuntu/etc/nginx/conf/

# 停止和移除
docker stop 67e
docker rm 67e
```



修改配置，在nginx.conf的http {} 中补以下配置：

```
# 显示目录
autoindex on;
# 显示文件大小
autoindex_exact_size on;
# 显示文件时间
autoindex_localtime on;
server{
	  listen 8888;
    server_name localhost ;
	  # 本地文件路径
	  root  /data;
}
```



```
# 重新启动nginx
docker run --name nginx -p 8888:8888  \
	-v /home/ubuntu/etc/nginx/nginx.conf:/etc/nginx/nginx.conf \
	-v /home/ubuntu/etc/nginx/logs/:/var/log/nginx/ \
	-v /home/ubuntu/etc/nginx/conf/:/etc/nginx/conf.d \
	-v /home/ubuntu/data/nginx/:/data \
	--privileged=true -d nginx
```

参考： https://www.cnblogs.com/chuyi-/p/15201718.html



#### 3 设置代理

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



#### 4 重定向到443

1. 去[百度云](https://cloud.baidu.com/product/ssl.html)平台购买证书
2. 创建实例的时候，选择 TrustAsia --> 域名型DV --> 单域名版，这样的证书不要钱







