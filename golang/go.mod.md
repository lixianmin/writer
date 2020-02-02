

```shell

# 创建一个go.mod文件
go mod init

# 如何引入一个新的库？
# 1. 写入完整import路径
# 2. option + enter 导入
```



当修改了loom库之后，如何更新到项目中？

```shell
# 1. 先更新依赖库（由于更新需要访问golang.org，因此shell需要翻墙）
go get -u github.com/lixianmin/gocore/loom

# 2.将所有的依赖库都加入到项目中的vendor目录下
go mod vendor 
```



如果刚刚更新的代码，无法在项目中下载下来，则可以修改go.mod文件，在require的路径后面加入git中的commit号。接下来再执行`go mod vendor`

```ruby
require (
	github.com/lixianmin/logo 5780d07   // 至少给7位数
)
```



