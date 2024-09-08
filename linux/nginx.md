



#### 1 Basics

##### 1 导论

1. 反向代理, 7层负载
2. 正向代理在client配置, 用于隐藏client; 反向代理在server配置, 用于隐藏server; 透明代理配置在网络中间设备上, 对双方都透明



##### 2 常用命令

1. vim /etc/nginx/nginx.conf 编辑conf文件
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



##### 3 docker搭建nginx

```shell
docker run --rm --name nginx -p 80:80 -d nginx:alpine
export DATA=/home/xmli/me/data

# 创建一些本机的配置目录
mkdir -p $DATA/nginx/etc/nginx/conf
mkdir -p $DATA/nginx/etc/nginx/logs
mkdir -p $DATA/nginx/data/nginx/html

# 将容器中的相应文件copy到刚创建的管理目录中
# 注：docker cp abc 中的 "abc" 为容器ID前缀，只要唯一就好了
docker cp abc:/etc/nginx/nginx.conf $DATA/nginx/etc/nginx/
docker cp abc:/etc/nginx/conf.d     $DATA/nginx/etc/nginx/conf/

# 停止和移除
docker stop abc

# 重新启动nginx
docker run --name nginx -p 80:80 \
	-v $DATA/nginx/etc/nginx/nginx.conf:/etc/nginx/nginx.conf \
	-v $DATA/nginx/etc/nginx/logs/:/var/log/nginx/ \
	-v $DATA/nginx/etc/nginx/conf/:/etc/nginx/conf.d \
	-v $DATA/nginx/data/:/data \
	--privileged=true -d nginx:alpine
	
	
# 部署静态html: 在http这个section中加入以下内容
    server {
        listen 80; 
        server_name localhost;

        root /data/html;
        index index.html;

        location / { 
            try_files $uri $uri/ /index.html;
        }   
    }   
```



参考： https://www.cnblogs.com/chuyi-/p/15201718.html



#### 2 常用模式

##### 1 Reverse Proxy

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



##### 2 Load Balancing

```nginx
upstream backend_servers {
  	ip_hash;	# 基于ip的确定性路由
    server backend1.example.com;
    server backend2.example.com;
    server backend3.example.com;
}

server {
    listen 80;
    server_name example.com;
    location / {
        proxy_pass http://backend_servers;
        proxy_set_header Host $host;
    }
}

```



##### 3 SSL Termination



```nginx

# 以下是nginx的代码
server {
  listen 443 ssl http2;
  server_name example.com;
  ssl_certificate /path/to/ssl/certificate;
  ssl_certificate_key /path/to/ssl/key;

  location / {
    proxy_pass http://backend;
  }
}
```



##### 4 Rate Limiting



```nginx
# ip粒度限流
http {
  limit_req_zone $binary_remote_addr zone=one:10m rate=10r/s;
  server {
    listen 80;
    server_name example.com;
    location / {
      limit_req zone=one burst=3;
      proxy_pass http://backend_server;
    }
  }
}
```



```nginx
# uid粒度限流
http {
  # Define a map that extracts the "userid" part of the cookie
  map $cookie_userid $userid {
    default "";
    ~^(?<id>[^;]+)(;.*)?$ $id;
  }

  # Use the "userid" variable in the rate limiting rules
  limit_req_zone $userid zone=one:10m rate=1r/s;

  server {
    listen 80;

    location / {
      # Rate limit requests based on the "userid" variable
      limit_req zone=one burst=5;
      # other directives
    }
  }
}

```





##### 5 动态缓存

1. 以往我们习惯于将缓存放在golang服务器内,



```nginx
http {
    proxy_cache_path /var/cache/nginx levels=1:2 keys_zone=my_cache:10m inactive=60m;
    proxy_cache_valid 200 302 1s;	# 最小单位 1s
    proxy_cache_valid 404     1m;

    server {
        listen 80;
        server_name example.com;

        location / {
            proxy_pass http://backend_server;
            proxy_cache my_cache;
            proxy_cache_key "$scheme$request_method$host$request_uri";
            proxy_cache_valid 200 302 10m;
            proxy_cache_valid 404 1m;
        }
    }
}
```



##### 6 File Server

修改配置，在nginx.conf的http {} 中补以下配置：

```nginx
# 显示目录
autoindex on;
# 显示文件大小
autoindex_exact_size on;
# 显示文件时间
autoindex_localtime on;
server {
	  listen 8888;
    server_name localhost ;
	  # 本地文件路径
	  root  /data;
}
```



##### 7 拒绝服务

```nginx
http {
    ...
    server {
        ...
        if ($load_average) {
            set $overload 0;
            if ($load_average > 0.9) {
                set $overload 1;
            }
            if ($overload) {
                return 503;
            }
        }
        ...
    }
    ...
}

```



