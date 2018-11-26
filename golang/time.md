



-----



| 方法                    | 描述                                    |
| ----------------------- | --------------------------------------- |
| func Add(duration) Time | 计算一个新的时间                        |
| func Sub(time) Duration | 计算一个时间差                          |
| t.Unix()                | 返回秒级时间戳，时间戳是与时区无关的    |
| t.Round(time.Minute)    | 按分钟取整，注意：time.Hour以上就不准了 |





---

MySQL如果要parse time的话，需要在连接的dsn中，添加parseTime=true 和loc=Local，此处的local可以换为具体的时区(Asia/Shanghai)



```go
db, err := sql.Open("mysql", 
    "bp:123456@tcp(10.13.4.161:3309)/bp_members?parseTime=true&loc=Local")
```



---

#### References

1. [互联网上的日期和时间](https://zhuanlan.zhihu.com/p/31829454)