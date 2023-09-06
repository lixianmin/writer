

##### 01 service

```shell
# service脚本用于管理 /etc/init.d/目录下的脚本
cat /etc/init.d/mysqld

service mysqld status
service mysqld start
service mysqld stop
service mysqld restart
service --status-all		# 查看所有服务的状态

# 
chkconfig --list 				# 显示所有被chkconfig管理的服务
chkconfig --add mysqld	# 将mysql加入管理
chkconfig --del mysqld
```

