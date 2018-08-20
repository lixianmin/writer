

-----

#### basics

1. 



```go
var wg sync.WaitGroup
// wg.Add(1)这一句要放到go之前，以确保在goroutine启动之前就被设置过了
wg.Add(1)
go func(wg *sync.WaitGroup) { // 传递wg对象的时候要传递指针，否则会被clone一份
    defer wg.Done()
}(&wg)
```

