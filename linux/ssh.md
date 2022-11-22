

```shell
# 生成签名文件（直接回车，不输入y）
ssh-keygen -t rsa
cat ~/.ssh/id_rsa.pub 

# 将自己的公钥copy到远程服务器，方便登陆
ssh-copy-id root@{IP}

# 如果证书是由远端生成的，则需要按以下方式登录
ssh ubuntu@12.34.56.78 -i ubuntu.pem

# 递归上传当前目录到服务器的指定目录
rsync -av -e ssh --exclude='.git' --exclude='logs' . root@{IP}:/root/xmli/tour-words
scp -r .  root@{IP}:/root/xmli/tour-words


# -t 默认情况下使用ssh执行远程命令并不会分配伪终端，但有些命令需要基于屏幕输入输出进行交互，此时可以使用-t参数分配一个伪终端，直到ssh命令执行结束
ssh root@xl-slave022 -t 'docker exec $(docker ps --filter "name=sqoop" --format "{{.ID}}") bash /scripts/import_all.sh'

```



```shell
# 下面的配置可以支持 ssh t8 登录
cat ~/.ssh/config

Host t8
HostName {IP}
User xmli
```





下面这些内容，需要放到.bashrc文件中，否则ssh远程执行的时候不会加载

```shell
########################################

export ENV=PROD # 根据不同的项目环境，设置为TEST或PROD
export EDITOR=vim

..()  { cd ..; pwd; ls -h --color; }
...() { cd ../..; pwd; ls -h --color; }

alias ll='/bin/ls -lh --color'
```



----

#### 2 FAQ

##### 1 Bad owener or permissions

```shell
chmod 600 ~/.ssh/config
```







