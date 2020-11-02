-----

#### 01  基础命令

1. 查询出来的结果，keys那些字段，左键点击可以弹出一个下拉菜单，执行udpate, query, rename等操作





------

#### 02 FAQ列表

##### 01 模糊查询

```json
// json模式下
{
  "username": {"$regex":"lixian.*", "$options":"i"}
}

// array模式下
array (
  'username' => new MongoRegex ("/lixian.*/i"),
)

```



##### 02 条件查询

```json
// json模式下
// $gt   >
// $gte  >=
// $lt   <
// $lte  <=
// $ne   !=
// $in : in 
// $nin: not in 
// $all: all 
// $not: 反匹配

{
  "createTime": {"$gte": 0}
}
```



##### 03 字段是否存在

```json
{
    "items": {"$exists":true}
}
```



----

#### 09 References

1. [【MongoDB 管理工具】RockMongo使用](https://my.oschina.net/lienson/blog/1570025)
2. [RockMongo使用方法](http://www.yuedun.wang/blogdetail/591ea6966af6717b614f9b2b)