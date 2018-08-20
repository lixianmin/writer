



----

控制对同一个map在不同goroutine中的read/write可以使用RWLock

```go
var lock sync.RWMutex
// write
lock.Lock()
lock.Unlock()

// read
lock.RLock()
lock.RUnlock()
```

