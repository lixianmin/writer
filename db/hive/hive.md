





---



|                                                           |                                                     |
| --------------------------------------------------------- | --------------------------------------------------- |
| /config/env                                               | 只有ubuntu （hive65）那个包里有，不是说没有打成功么 |
|                                                           |                                                     |
| docker-compose.yml在哪，docker 启动命令是什么             |                                                     |
| yuanxulei/hadoop:2.6.0-cdh5.15.0                          | 这个里没有hive: hive64, hive65, hive66, hive67      |
| yuanxulei/hue:3.9.0-sqoop1.4.6-hive1.1.0-cdh5.15.0-ubuntu | 这个里hive指令， hive65                             |
|                                                           |                                                     |
|                                                           |                                                     |
|                                                           |                                                     |



1. `yum install tig` ，或 `apt-get instal tig`
2. 数据存储位置：/data/hadoop
3. import_all.sh脚本是在docker里面执行的， ubuntu那个docker
4. Ambari





----

hdfs指令

```bash
# 从本机或hdfs上的文件拷贝到到hdfs中
hdfs dfs -put /from/local/file /to/hdfs/filename
hdfs dfs -put hdfs:///hive/file /to/hdfs/filename

```



hive指令

```bash
# -e 从命令行拿到sql语句
# -f 从数据库拿到sql语句
# -S silent mode
hive -e "CREATE DATABASE IF NOT EXISTS $database;" -S
```



-------

1. How to quickly setup a Hadoop cluster in Docker | 鱼喃
1. https://blog.newnius.com/setup-apache-hive-in-docker.html
2. https://blog.newnius.com/how-to-quickly-setup-a-hadoop-cluster-in-docker.html#Start-Hadoop-Cluster
3. https://sqoop.apache.org/docs/1.4.7/SqoopUserGuide.html

