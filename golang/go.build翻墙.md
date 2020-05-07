

命令行执行以下语句似乎就可以了：


``` shell
go env -w GOPROXY=https://goproxy.cn,direct
go env -w GOPRIVATE=*.gitlab.com,*.gitee.com
go env -w GOSUMDB=off
```





----

#### 0x09 References

1. [go proxy 设置](https://www.jianshu.com/p/e0c878d4ca19)
2. 