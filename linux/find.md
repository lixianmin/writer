

```shell

# -amin, -atime access时间
# -cmin, -ctime change时间
# -mmin, -mtime modify时间
find /etc -ctime +10  # 10天之【前】创建的
find /etc -ctime -10  # 10天之【内】创建的 
find /etc -mmin  +30	# 30分钟之【前】修改过的
find /etc -mmin  -30  # 30分钟之【内】修改过的

# 删除修改时间在60分钟之【前】文件
find /logs/202* -type f -mmin +60 -exec rm {} \;

# 找到空文件夹
find . -type d -empty

# 删除所有的空文件夹
find . -type d -empty -delete
```

