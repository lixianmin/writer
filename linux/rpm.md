

```sh
# 安装
rpm -ivh *.rpm						# -i install, -v verbose, -h hash marks

rpm -ivh a.rpm --nodeps		# 忽略依赖关系，强行安装
rpm -ivh a.rpm --force    # 忽略软件包及文件的冲突，重新安装

rpm -ivh a.rpm --test			# 只检查是否可以安装，但并不安装
rpm -ivh --tset a.rpm

# 查询
rpm -qpi a.rpm 						# --query --package --install 

# 删除
rpm -e a     							# -e erase，不能带 .rpm 后缀
rpm -e lynx --nodeps			# 忽略依赖检查


```

