

#### 01 安装

1. 根据这个文档：[2.2 虚拟机/物理机部署](https://ctripcorp.github.io/apollo/#/zh/deployment/distributed-deployment-guide?id=_22-虚拟机物理机部署)， 去github上面下载最新的安装包，分别apollo-adminservice, apollo-configservice, apollo-portal，分别新建三个目录，adminservice, configservice, portal，将三个zip包mv到对应目录下， unzip之
2. 根据这个文档：[2.1 创建数据库](https://ctripcorp.github.io/apollo/#/zh/deployment/distributed-deployment-guide?id=_21-创建数据库)，下载apolloportaldb.sql和apolloconfigdb.sql，打开`mysql -uroot -p`，通过source命令直接导入两个sql文件
3. `select user, host from mysql.user;` 展示当前用户列表
4. 
5. 分别进入 adminservice/config, configservice/config, portal/config目录下，找到application-github.properties文件，修改apollo登录MySQL使用的用户名和密码
6. 

```


```





#### 02 多环境部署

```bash
# 在portal中：管理员工具 -> 系统参数
输入:         apollo.portal.envs
将dev修改为:   dev,test,prod

# 加meta地址
vi portal/config/apollo-env.properties



```





1. [配置多个环境](https://ctripcorp.github.io/apollo/#/zh/deployment/distributed-deployment-guide?id=_1-apolloportalenvs-%e5%8f%af%e6%94%af%e6%8c%81%e7%9a%84%e7%8e%af%e5%a2%83%e5%88%97%e8%a1%a8)
2. [Portal如何增加环境？](https://ctripcorp.github.io/apollo/#/zh/faq/common-issues-in-deployment-and-development-phase?id=_4-portal如何增加环境？)

