

命令行执行以下语句似乎就可以了：


``` shell
go env -w GOPROXY=https://goproxy.cn,direct
go env -w GOPRIVATE=*.gitlab.com,*.gitee.com
go env -w GOSUMDB=off
```



```shell
# 如果go build 找不到远程的repository了，就像okex-coin一样，则可以直接使用vendor中的代码：
go build -mod vendor
```



----

#### 0x09 References

1. [go proxy 设置](https://www.jianshu.com/p/e0c878d4ca19)
2. 