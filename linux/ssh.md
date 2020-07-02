



```shell
# 生成签名文件
ssh-keygen -t rsa
cat ~/.ssh/id_rsa.pub 

# 将自己的公钥copy到远程服务器，方便登陆
ssh-copy-id root@180.76.55.113

# 递归上传当前目录到服务器的指定目录
rsync -av -e ssh --exclude='.git' --exclude='logs' . root@180.76.55.113:/root/lixianmin/tour-words
scp -r .  root@180.76.55.113:/root/lixianmin/tour-words

```

