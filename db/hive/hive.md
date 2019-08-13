





---

#### 0x01 hive项目相关

|                     |                    |
| ------------------- | ------------------ |
| rsync -avz .. /data | 这一句好像没有用吧 |
|                     |                    |
|                     |                    |



1. `yum install tig` ，或 `apt-get instal tig`
2. 项目位置：/root/hive
3. 数据位置：/data/hadoop
4. import_all.sh脚本是在docker里面执行的， ubuntu那个docker
5. Ambari
6. 01_setup.sh等部署脚本都是hadoop主节点xl-slave021上执行的，执行的当前目录是/data/scripts
7. docker service create的时候通过mount做的文件映射关系



----

#### 0x02 hive命令

```bash
# -e 从命令行拿到sql语句
# -f 从数据库拿到sql语句
# -S silent mode
hive -e "CREATE DATABASE IF NOT EXISTS $database;" -S
```



----

#### 0x03 sql

```mysql
# 可以删除分区
alter table account_record drop if exists partition (dt = 20190726);

# hive中我们使用string记录时间，而时间精度到0.1s，因此如果要使用精度为时分秒的比较，一定要使用.0结尾的字符串，否则会遗漏数据；只比较年月日则不存在这样的问题
select * from account_record where create_time >= '2019-07-26'
select * from account_record where create_time >= '2019-07-26 00:00:00.0'

```



-------

1. How to quickly setup a Hadoop cluster in Docker | 鱼喃
1. https://blog.newnius.com/setup-apache-hive-in-docker.html
2. https://blog.newnius.com/how-to-quickly-setup-a-hadoop-cluster-in-docker.html#Start-Hadoop-Cluster
3. https://sqoop.apache.org/docs/1.4.7/SqoopUserGuide.html

