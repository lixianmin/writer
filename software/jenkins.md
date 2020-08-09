

----

#### 0x01 安装

```shell
# 解决以下问题：
# Can not write to /var/jenkins_home/copy_reference_file.log. Wrong volume permissions?
# touch: cannot touch ‘/var/jenkins_home/copy_reference_file.log’: Permission denied
chown -R 1000:1000 /data/jenkins

# 获取中国定制版的镜像，否则初始化jenkins总是失败
docker pull jenkinszh/jenkins-zh:latest

# 运行jenkins
docker run -d -p 8080:8080 -p 50000:50000 -v /data/jenkins:/var/jenkins_home jenkinszh/jenkins-zh:latest

# 通过docker exec -it container-id bash 进入docker，查看初始化密码
# cat /var/jenkins_home/secrets/initialAdminPassword
cat /data/jenkins/secrets/initialAdminPassword

# 在『自定义Jenkins』页，选择『选择插件来安装』，在『Source code Management』页加入对github支持


```





----

1. [jenkins docker image](https://hub.docker.com/r/jenkinszh/jenkins-zh)

2. [Docker 安装 Jenkins , 并解决初始安装插件失败](https://www.cnblogs.com/stormlong/p/12784513.html)

