

----

#### mysqldump



1. man mysqldump	查看帮助文档
2. 



| 参数                 | 描述                                                         |      |
| -------------------- | ------------------------------------------------------------ | ---- |
| -p                   | 指手动输入密码，命令行本身中不包含密码字符串                 |      |
| --no-data -d         | 只导出表结构，不导出数据                                     |      |
| --quick              | 用于备份大表，强制一次只取一行数据，而不是将整个数据集缓存到内存中 |      |
| --single-transaction | 1. 备份前执行start transaction命令，因此只能用于innodb表；<br />2. 备份大表时，请结合 --quick参数使用 |      |
|                      |                                                              |      |



```shell
# 从库:备份命令，只备份数据，不备份表结构
# 需要加--single-transaction，否则会调用lock tables，没有权限
# 需手动输入密码： staging_db_read
mysqldump --single-transaction -h rr-6welgnc4q0p22134x.mysql.japan.rds.aliyuncs.com -u staging_db_read -p coinbene_exchange trade_detail > trade_detail_2019-07-22.sql

# 只导出表结构
mysqldump --single-transaction -h rr-6welgnc4q0p22134x.mysql.japan.rds.aliyuncs.com -u staging_db_read -p --no-data coinbene_exchange trade_detail > trade_detail_table.sql


# 恢复表结构
# 需手动输入密码： XLu6MjqVijQoR8yGJSzo
mysql -h staging-mysql.mysql.japan.rds.aliyuncs.com -u root -p coinbene_risk  < trade_detail_table.sql

# 恢复数据
# 5.8GB的数据，导出很快，但导入好像花了十几分钟以上
mysql -h staging-mysql.mysql.japan.rds.aliyuncs.com -u root -p coinbene_risk  < trade_detail_2019-07-22.sql
```



---

#### 0x09 References

1. [基于mysqldump做备份恢复](https://jkzhao.github.io/2018/04/21/%E5%9F%BA%E4%BA%8Emysqldump%E5%81%9A%E5%A4%87%E4%BB%BD%E6%81%A2%E5%A4%8D/) 
2. 