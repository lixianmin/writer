

```shell

sudo yum install postfix
vi /etc/postfix/my.conf

sudo service postfix start
sudo chkconfig --add postfix

# 查看状态
sudo service postfix status

# 发送测试邮件
echo "body" | mail -s "subject" lixianmin@live.cn
```

