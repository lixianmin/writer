

------

#### map

1. 访问不存在的key，默认返回0值，不会引发错误，但推荐使用ok idiom模式
2. 在迭代期间删除或新增键值是安全的
3. 删除操作仅仅将对应的tophash[i]设置为empty，并非释放内存，因此map的delete并非真的delete，所以对迭代器是没有影响的，所以在迭代过中删除是安全的。
4. 在不同的goroutine中对同一个map分别read/write是不安全的，需要进行同步



```go
	// 定义一个长度为0的map, 后面的{}是用于存放初始值的地方
    var dict = map[string]int{}
    dict = map[string]int{"hello": 1, "world": 2}
    // 直接创建一个map
    dict = make(map[string]int)

	fmt.Printf("------------type=%T, len(slice)=%v\n", slice, len(slice))
	for i, v := range slice {
		fmt.Printf("%d = %d\n", i, v)
		dict[strconv.Itoa(v)] = v
	}

	fmt.Printf("------------type=%T, len(dict)=%v\n", dict, len(dict))
	key := "hello"
	dict[key] = 2
	delete(dict, key)

	// 从map 中读取某个不存在的键时，结果是 map 的元素类型的零值
	fmt.Printf("value of key : %v\n", dict[key])
	if v, ok := dict[key]; ok {
	}

	for k, v := range dict {
	}

```



---

#### sync.Map

1. 无len(map)这样的方法
2. 无类型安全
3. Range()遍历的数据可能并**不对应任何一次Map内容的快照**
4. 



------

#### References

1. [Go 1.9 sync.Map揭秘](https://colobu.com/2017/07/11/dive-into-sync-Map/)
2. [The new kid in town — Go’s sync.Map](https://medium.com/@deckarep/the-new-kid-in-town-gos-sync-map-de24a6bf7c2c)