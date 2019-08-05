

-----

#### 0x01 hadoop命令



```shell
# 打印任务列表
hadoop job -list

# 杀死任务
hadoop job -kill job_1563330554479_0004

# 查看任务详情
hadoop job -status job_1563330554479_0006

# 重启hadoop：进入hadoop所在的docker，然后执行以下命令
stop-all.sh
start-all.sh
```



---

#### 0x02 FAQ



##### 1. 如何重新yarn-site.xml



```shell
# 1. 登陆到slave021主节点，使用06_git_pull_rsync_import.sh更新并分发yarn-site.xml到各节点
06_git_pull_rsync_import.sh

# 2. 使用docker service update --force hadoop-node02x命令重启和节点
docker service update --force hadoop-node021
docker service update --force hadoop-node022
docker service update --force hadoop-node023
docker service update --force hadoop-node024
docker service update --force hadoop-node025
```



##### 2. hadoop支持多任务

因为hadoop可能一直在导入数据，因此，其它任务有可能直接卡死，表现为在hive中提交一个sql后，直接卡在：

```
Starting Job = job_1563527626263_0001, Tracking URL = http://hadoop-node021:8088/proxy/application_1563527626263_0001/

Kill Command = /usr/local/hadoop/bin/hadoop job  -kill job_1563527626263_0001
```

然后长时间无响应。



解决方案是让hadoop支持多任务：

使用CapacityScheduler调度器：

1. 在yarn-site.xml中加入 yarn.resourcemanager.scheduler.class 




```xml
<property>
    <name>yarn.resourcemanager.scheduler.class</name>
 <value>org.apache.hadoop.yarn.server.resourcemanager.scheduler.capacity.CapacityScheduler</value>
</property>
```



参考文献：

1. [Hadoop 2.x Capacity Scheduler](https://mapr.com/docs/52/AdministratorGuide/Hadoop2.xCapacityScheduler-SetUpRM.html)
2. 



~~以下使用FairScheduler的尝试失败了~~

1. 需要将hadoop-fairscheduler*.jar这个jar包copy拷贝到$HADOOP_HOME/lib下面，具体是在创建镜像的dockerfile中加入以下一行：

```shell
RUN cp `find $HADOOP_HOME/ -name "hadoop-fairscheduler*.jar"|head -1` $HADOOP_HOME/lib
```



2. 在mapper-site.xml中加入FairScheduler，具体是修改 `vi $HADOOP_CONF_DIR/mapred-site.xml`

```xml
<property>
  <name>mapred.jobtracker.taskScheduler</name>
  <value>org.apache.hadoop.mapred.FairScheduler</value>
</property>
```

