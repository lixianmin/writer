

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
docker run -d -p 8080:8080 -p 50000:50000 -v /data/jenkins:/var/jenkins_home -v /etc/localtime:/etc/localtime:ro -e JAVA_OPTS=-Duser.timezone=Asia/Shanghai jenkinszh/jenkins-zh:latest

# 通过docker exec -it container-id bash 进入docker，查看初始化密码
# 或者：cat /var/jenkins_home/secrets/initialAdminPassword
cat /data/jenkins/secrets/initialAdminPassword

# http://jenkins-address:8080 打开jenkins
# 在『自定义Jenkins』页，选择『选择插件来安装』，在『Source code Management』页加入对github支持

# 在github主页，点击『右上角个人图标』，生成token，这个在jenkins中叫secret text 
1. Settings --> Developer settings --> Personal access tokens -> Generate new token
2. 选中repo 和 admin:repo_hook
3. 点击 Generate Token --> 记住这个token，否则再也找不回来了
4. 如果使用github的『用户名/密码』的方式访问，则不需要使用这个token

# 转到github的具体项目
Settings --> Webhooks --> Add Webhook --> 输入刚刚部署jenkins的服务器的IP：
http://jenkins-address:8080/github-webhook/  --> 『注意：最后以/结尾，不能少』

不需要调整Context type或加入Serect等东西

# 配置jenkins
1. 首页左边 --> 系统管理 --> 系统配置 --> GitHub --> 添加Github服务器
	1. 在GitHub Server这一内嵌面板中，『名称』随便填
	2. API URL就是https://api.github.com，不用改
	3. 凭据使用Secret text，点击『添加』，类型选『Secret text』，『范围』不改，『Secret』中粘贴在github上生成的personal access token，其它的『ID』『描述』不填


# 进入docker，将签名文件公钥copy到远程服务器
# 生成签名文件
# ssh-keygen -t rsa
# cat ~/.ssh/id_rsa.pub 
ssh-copy-id root@192.168.64.5

# 在『构建』的部分添加远程执行shell的命令
# ssh远程执行命令时，可能会碰到缺少环境变量的问题，记得将环境变量放到.bashrc中（而不是.bash_profile）
ssh root@192.168.64.5 /root/go/src/github.com/lixianmin/tour-server/build.jenkins.sh

```





----

1. [Jenkins与Github集成 webhook配置](https://blog.csdn.net/qq_21768483/article/details/80177920)
2. [jenkins docker image](https://hub.docker.com/r/jenkinszh/jenkins-zh)
3. [Docker 安装 Jenkins , 并解决初始安装插件失败](https://www.cnblogs.com/stormlong/p/12784513.html)
4. [从零开始搭建JENKINS+GITHUB持续集成环境【多图】](https://juejin.im/post/6844903992833605640)
5. 

