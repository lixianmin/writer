----

#### 1 Basics



1. cron的日志在 /var/log/cron中



| 符号 | 含义                                                         |
| ---- | ------------------------------------------------------------ |
| *    | 代表所有的取值范围内的数字，如月份字段为*，则表示1到12个月   |
| /    | 代表时间间隔，如分钟字段为*/10，表示每10分钟执行1次          |
| -    | 闭区间。如“2-5”表示“2,3,4,5”，小时字段中0-23/2表示在0~23点范围内每2个小时执行一次 |
| ,    | 分散的数字（不需要连续），如1,2,3,4,7,9。                    |



```shell
# linux下查看crontab表达式的格式
# crontab的时间分5部分，分别是：分时天月周
cat /etc/crontab

# Example of job definition:
# .---------------- minute (0 - 59)
# |  .------------- hour (0 - 23)
# |  |  .---------- day of month (1 - 31)
# |  |  |  .------- month (1 - 12) OR jan,feb,mar,apr ...
# |  |  |  |  .---- day of week (0 - 6) (Sunday=0 or 7) OR sun,mon,tue,wed,thu,fri,sat
# |  |  |  |  |
# *  *  *  *  * user-name  command to be executed


# 编辑当前用户的crontab文件
crontab -e

# 列出当前用户的crontab
crontab -l

# 查看crontab执行的日志
tail -f /var/log/cron

# crontab示例
* * * * * command						# 每分钟执行
3,15 * * * * command				# 每小时的第3和第15分钟执行
3,15 8-11 * * * command			# 每天上午8-11点的第3和15分钟执行command
3,15 8-11 */2 * * command		# 每隔2天的上午8-11点的第3和15分钟执行command
30 21 * * * /etc/init.d/smb restart	# 每晚的21:30重启smb
* */1 * * * /etc/init.d/smb restart	# 每一小时重启smb
```



crontab是周期性指令，如果每一轮执行结束都要依赖于上一轮执行结束的话，需要加阻塞策略

```shell
# flock是标准C的方法
man flock

# 阻塞模式
# -s Shard lock
# -x eXclusive lock
# -n Nonblock mode
# -c 执行一个指令
# /tmp/run.lock这个文件如果不存在的话，会直接创建
* * * * * flock -x /tmp/run.lock -c command

# 非阻塞，直接失败模式
* * * * * flock -xn /tmp/run.lock -c
```



```shell
# 如果要在crontab中执行sudo命令, 则要使用
sudo crontab -e

# 加入以下指令, 每10分钟同步一次系统时间
*/10 * * * * sudo hwclock -s
```





-------

#### 2 安装

```shell
yum install vixie-cron
service crond start
chkconfig crond on
```





---

#### 9 References

1. [cron表达式在线生成工具](http://www.bejson.com/othertools/cron/)
2. [crontab用法与实例](https://www.linuxprobe.com/how-to-crontab.html)
3. 