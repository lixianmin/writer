

#### 01 安装

1. 根据这个文档：[2.2 虚拟机/物理机部署](https://ctripcorp.github.io/apollo/#/zh/deployment/distributed-deployment-guide?id=_22-虚拟机物理机部署)， 去github上面下载最新的安装包，分别apollo-adminservice, apollo-configservice, apollo-portal，分别新建三个目录，adminservice, configservice, portal，将三个zip包mv到对应目录下， unzip之
2. 根据这个文档：[2.1 创建数据库](https://ctripcorp.github.io/apollo/#/zh/deployment/distributed-deployment-guide?id=_21-创建数据库)，下载apolloconfigdb.sql和apolloportaldb.sql（生产环境只需要部署一个），打开`mysql -uroot -p`，通过source命令直接导入两个sql文件



```mysql
create user 'apollo'@'%' identified by 'password';
grant all privileges on ApolloConfigDB.* to "apollo"@'%';
# grant all privileges on ApolloPortalDB.* to "apollo"@'%';

# 打印用户列表
select user, host from mysql.user; 

source /home/batsdk/software/apollo1.7.1/apolloconfigdb.sql
# source /home/batsdk/software/apollo1.7.1/apolloportaldb.sql
```



```bash
# 修改apollo登录MySQL使用的用户名和密码
vi adminservice/config/application-github.properties
vi configservice/config/application-github.properties
#vi portal/config/application-github.properties

# 打开以下文件:
# 1. 修改LOG_DIR，日志需要写入到有权限的目录中
# 2. 修改端口，有可能8090, 8080, 8070这样的端口已经被占用了
vi adminservice/scripts/startup.sh
vi configservice/scripts/startup.sh
#vi portal/scripts/startup.sh

# 启动三个项目
adminservice/scripts/startup.sh
configservice/scripts/startup.sh
#portal/scripts/startup.sh	# portal可以只启动一个

# 查看三个服务占用的ip端口，分别是 adminservice 8090, configservice 8080, portal 8070
netstat -ntlp | grep java

# 到portal所在的服务器，修改meta-service地址
# 注意，这里只能加apollo内置的环境：dev, fat, uat, pro，否则需要修改apollo的代码
vi portal/config/apollo-env.properties 

# 重启portal服务
./portal/scripts/shutdown.sh
./portal/scripts/startup.sh
```



#### 02 多环境部署

```bash
# 在portal中：管理员工具 -> 系统参数
输入:         apollo.portal.envs
将dev修改为:   dev,fat,pro

```



1. [配置多个环境](https://ctripcorp.github.io/apollo/#/zh/deployment/distributed-deployment-guide?id=_1-apolloportalenvs-%e5%8f%af%e6%94%af%e6%8c%81%e7%9a%84%e7%8e%af%e5%a2%83%e5%88%97%e8%a1%a8)
2. [Portal如何增加环境？](https://ctripcorp.github.io/apollo/#/zh/faq/common-issues-in-deployment-and-development-phase?id=_4-portal如何增加环境？)

