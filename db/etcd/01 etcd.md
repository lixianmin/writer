

-----

#### 0x01 安装

```shell
brew install etcd
brew services start etcd
```



-----

#### 0x02 基本命令

```shell
# 添加root用户，会提示输入密码，示例使用123456
# root用户默认拥有root权限
etcdctl user add root

# 开启验证
etcdctl --user="root" --password="123456" auth enable
```



-----

#### 0x09 References

1. https://github.com/etcd-io/etcd
2. 

