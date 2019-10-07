

```shell

# 创建一个go.mod文件
go mod init

# 如何引入一个新的库？
# 1. 写入完整import路径
# 2. option + enter 导入

# 将所有的依赖库都加入到项目中的vendor目录下
go mod vendor 

# 更新依赖库
go get -u github.com/lixianmin/gocore/loom
```

