

1. linux下所有的脚本语言都必须支持#方式的注释，比如python, ruby, shell
2. python没有块级作用域，因此下面的代码可以运行成功

```python
if 1 == 1:
    name = "lzl"

print(name)

for i in range(10):
    age = i
 
print(age)
```

2. numpy.ndarray的切片与普通的list的切片操作概念是不一样的
   - ndarray的切片只是对ndarray中原始数据的一份视图（引用），因此对切片所有的改动都会反应到原始的ndarray上；如果想得到ndarray的切片副本，就要显示的复制，例如arr[5:8].copy()
   - list的切片产生的是一个新的list对象，对切片内容的赋值只会修改切片自身的数据，不会修改原始的list



----

#### References

1. [Python 变量作用域](https://blog.csdn.net/cc7756789w/article/details/46635383)
2. 