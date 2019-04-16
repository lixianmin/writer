

-----

#### 0x01 Basics



| 名称       | 描述                                         |      |
| ---------- | -------------------------------------------- | ---- |
| Discover   | 查看、测试数据源                             |      |
| Visualize  | 创建自己的数据视图                           |      |
| Dashboard  | 将创建好的数据视图集成到这里面去，形成仪表盘 |      |
| Timelion   | 跟时间序列相关的                             |      |
| DevTools   | Console，直接写lucene表达式                  |      |
| Management | 管理index                                    |      |



----

#### 0x02 Question&Answer



##### 创建仪表盘流程

在Visualize中创建并保存，在Dashboard中加入并使用



##### 如何下载CSV数据

在Dashboard中，找到对应的图表，点击右上角，选择Inspect，选择download csv



##### 计算次留

1. 需要在创建user_behaviour表时预留retain_days字段，在输入数据时直接将这个字段计算完成放到es中
2. 在timelion中创建次留的表：

```shell
# 次留数量，其中 1553597349000是毫秒时间戳：
.es(index=user_behaviour,  timefield=create_time,  q='retain_days:>=1 AND register_time:>1553597349000' , metric=cardinality:user_name.keyword).bars()

# 次留的留存率：
.es(index=user_behaviour,  timefield=create_time, q='retain_days:>=1 AND retain_days:<=1' , metric=cardinality:user_name.keyword).bars().label('retain days1').divide(.es(index=user_behaviour,  timefield=create_time,  offset=-1d,  q='retain_days:>=0 AND retain_days:<=0' , metric=cardinality:user_name.keyword))
```



---

#### 0x03 注意事项

1. 查询条件里不能有**空格**，一点儿空格都不能用，比如metric='avg:FlightTimeMin'，写成 metric='avg: FlightTimeMin'就查不出来了
2. 在测试时，需要**选取时间区间**，有时候表达式是对的，但是时间区间内没有数据，就会看不到结果
3. string类型一般是不能进行aggregate操作的，比如user_name，但user_name.keyword可以
4. timelion的数据是不能导出的， 但其它的Visualize，都可以通过菜单栏的Inspect，打开数据页面后下载之



| 类型                  | 示例                                  |      |
| --------------------- | ------------------------------------- | ---- |
| aggreate操作          | es(split=user_name.key_word:5)        |      |
| 一个char中画多个graph | es(metric=min:byes, metric:max:bytes) |      |
| 多次查询              | es(q=GB, q=CA)                        |      |
|                       |                                       |      |



---

#### 0x04 Scripted Fields

1. Management--> Index Patterns --> Script Fields，创建script field
2. 选择painless language
3. scripted field 可以用到es(metric=avg:xx)中， 但不能用在es(q=xxx)中，如果**需要按条件进行过滤的话**，只能使用普通的field



```js
// 可以计算时间相关的内容，支持三目运算符
retain_day1 = (doc['create_time'].value.getMillis() - doc['register_time'].value.getMillis())/ 1000 / 60 / 60 / 24 > 0 ? 1: 0

// 甚至可以写java代码
```



---

#### 0x09 References

1. [timelion一些基本表达式](https://www.jianshu.com/p/7cd9b3634131)
2. [Kibana 用户指南（Timelion入门）](https://segmentfault.com/a/1190000016679290)
3. [numeric field api](https://www.elastic.co/guide/en/elasticsearch/reference/current/modules-scripting-expression.html#_numeric_field_api)
4. [query string syntax](https://www.elastic.co/guide/en/elasticsearch/reference/6.7/query-dsl-query-string-query.html#query-string-syntax)
5. [Timelion recepies](https://vkroz.github.io/kibana_cookbook/html/timelion.html)
6. 

