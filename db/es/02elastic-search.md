



----

#### 1 Basics

```shell
# 启动服务
brew services list
brew services start elasticsearch
brew services start kibana
```



1. **{}代表hash表（对象），子节点的name必须不一样；[]代表list，子节点的name可以相同；比如bool是hash，而must是list**
2. filter, should, must是**条件节点（FSM），都是[]**，bool与term, range, ids, match是**查询节点（TRIM），都是{}**。**控制节点中只能包含查询节点，查询节点中只能包含控制节点**。如果控制节点中想包含另一个控制节点，则中间必须加一层查询节点（只能是bool）。
3. index => database, type => table, document => row
4. kibana的x-pack本身有shield（身份认证）与reporting（csv下载）的功能，但是在免费版本中没有
5. 



|         |                          |      |
| ------- | ------------------------ | ---- |
| _bulk   | 指上传数据               |      |
| _search | 搜索数据                 |      |
| _source | 查询结果中实际的数据部分 |      |
|         |                          |      |



|       |                                                              |      |
| ----- | ------------------------------------------------------------ | ---- |
| aggs  | 聚合操作                                                     |      |
| query | 查询                                                         |      |
| size  | 1. 限制返回的hits.hits数组的长度，默认为10，相当于SQL中的limit<br />2. 执行aggs操作时，因为不需要原始值，因此一般设置size=0 |      |
|       |                                                              |      |



-----

#### 2 索引操作





----

#### 3 数据操作



##### 01 增

1. 最好手动增加_mappings中的field，而不是第一次上传数据时自动创建，因为field类型可能不是你想要的
2. update数据的方式与新增数据的方式相关，指定_id即可



```json
# 上传数据，自动生成_id
POST test_index/person
{"apkVersion":"world"}

# 手动指定_id。比如我已经在mongodb中生成了一个id，这时手动指定_id有两个好处：
# 1. 方便mongo中的数据与es中的数据做关联
# 2. 通过primary index去重，解决kafka重试问题
#
# 注：经过验证，这里使用post/put请求都可以
POST/PUT test_index/person/603606980695e0a74b360230
{"apkVersion":"hello"}

```



##### 02 删

```js
POST test_index/_delete_by_query 
{
  "query":{
     "term":{ "apkVersion":"world" }
  }
}
```



##### 03 查

1. **务必使用POST**：虽然GET也可以查，但后面body这个json是不起作用的，GET需要把所有的字段写到query string中
2. 查询一定是query开头的， 下面使用match, term等叶节点是直接查询， 如果需要复合查询可以使用bool节点



```js
# 查询所有数据
POST test_index/_search?pretty
{}

# 查询mongoid字段不为空的
POST test_index/_search?pretty
{"query":{"exists":{"field":"mongoid"}}}

等价于 query_string里加上： _exists_:mongoid
等价于 wildcard: { mongoid: "*" }


# 查询特定的term
# 按_id查询的方式，与按普通字段的查询方式相同，在match/term叶节点下面指定_id的值即可
POST test_index/person/_search
{
    "query": {
        "match" : {"apkVersion" : "hello"}
    }
}


{
  "query": {
    	 "term":{"apkVersion": "hello" }
  }
}


{
  "query": {
    "bool":{
    		"filter": {
    		 "term":{"apkVersion": "hello" }
    	}
    }
  }
}

```





-----

#### 4 叶子查询

1. 叶子查询是指**内部节点是field的节点**，通常是**term（精确查询）和match（模糊查询）**
2. field节点的子节点可以有一些参数用于约束叶子查询的行为
3. 简记为TRIM，分别代表Term, Range, Ids, Match



##### 01 [term](https://www.elastic.co/guide/en/elasticsearch/reference/7.9/query-dsl-term-query.html)

```json
GET /_search
{
  "query": {
    "term": {
      "user.id": {
        "value": "kimchy",	// 必选字段
        "boost": 1.0				// 调整relevance score
      }
    }
  }
}
```

1. 精确匹配字段，一般用于price, price id, username, appKey等可以精确匹配的场景
2. 避免对text类型的字段使用term查询。因为es对text类型的字段会做预处理（比如删除标点符号、分词、转换成小写等），所以很难使用term子句精确匹配，建议使用match子句。



##### 02 [terms](https://www.elastic.co/guide/en/elasticsearch/reference/7.9/query-dsl-terms-query.html)

1. term查询的复数形式
2. 多个参考值之间是or的关系



##### 03 [range](https://www.elastic.co/guide/en/elasticsearch/reference/7.9/query-dsl-range-query.html)

```json
GET /_search
{
  "query": {
    "range": {
      "timestamp": {
        "gte": "now-1d/d",
        "lt": "now/d"
      }
    }
  }
}
```



##### 04 [IDs](https://www.elastic.co/guide/en/elasticsearch/reference/7.9/query-dsl-ids-query.html)

```json
GET /_search
{
  "query": {
    "ids" : {
      "values" : ["1", "4", "100"]
    }
  }
}
```

1. 使用_id字段查询



##### 05 [match](https://www.elastic.co/guide/en/elasticsearch/reference/7.9/query-dsl-match-query.html)

```json
GET /_search
{
  "query": {
    "match": {
      "message": {
        "query": "this is a test",
        "operator": "and"
      }
    }
  }
}
```



1.  query参数描述搜索词列表，默认行为与baidu类似，以or关系按评分返回匹配的document
2.  如果需要改为and的关系，则需要加一个`"operator":"and"`的参数



##### 06 [exists](https://www.elastic.co/guide/en/elasticsearch/reference/7.9/query-dsl-exists-query.html)

存在性查询

```json
GET /_search
{
    "query": {
        "exists" : { "field" : "apkVersion" }
    }
}

```



-----

#### 5 [bool复合查询](https://www.elastic.co/guide/en/elasticsearch/reference/7.9/query-dsl-bool-query.html)



1. bool查询指多个**子条件必须同时成立**，是&&操作
2. **bool实现为hash表**，因此多个子条件必然是不一样的，不会同时出现2个must或2个filter
3. 支持must, filter, should, must_not这4个子句
4. minimum_should_match用在should子句后面



```json
POST _search
{
  "query": {
    "bool" : {
      "must" : {
        "term" : { "user.id" : "kimchy" }
      },
      "filter": {
        "term" : { "tags" : "production" }
      },
      "must_not" : {
        "range" : {
          "age" : { "gte" : 10, "lte" : 20 }
        }
      },
      "should" : [
        { "term" : { "tags" : "env1" } },
        { "term" : { "tags" : "deployed" } }
      ],
      "minimum_should_match" : 1,
      "boost" : 1.0
    }
  }
}
```



##### 01 must

1. 必须匹配，must的**子节点之间是&&关系**
2. **must实现为list**，因此子节点中可以有多个term对象
3. must就是会影响score的filter
4. **must子节点可是bool查询**



```json
// 以下两个查询是等价的：
{
  "query":{
    "match": {
        "title": {
            "query":    "brown fox",
            "operator": "and"
        }
		}
  }
}

{
  "query":{
		"bool": {
    	"must": [
      	{ "term": { "title": "brown" }},
        { "term": { "title": "fox" }}
    	]
  }}
}
```



##### 02 filter

1. doc必须匹配，跟must类似但不影响score



##### 03 should

1. doc应该匹配should子句查询的一个或多个
2. 

```json
// 以下两个查询是等价的：
{
    "match": { "title": "brown fox"}	// 其中title是要查的field名称
}

{
  "bool": {
    "should": [
      { "term": { "title": "brown" }},
      { "term": { "title": "fox"   }}
    ]
  }
}
```



##### 04 must_not

1. doc必须不匹配条件



----

#### 6 aggs聚合操作

1. aggs是{}，下面的节点名是任意取的
2. aggs可以嵌套
3. order设置桶的排序，默认以doc_count降序排列。order的可选值有：
   1. `"order":{"_count":"asc"}`  对应使用doc_count
   2. `"order":{"_key":"asc"}`  在使用histogram或date_histogram的时候，对应桶的key
   3. 支持子聚合的结果作为排序字段
4. 



```json
{
  "aggs": {
    "group_by_ip": {
      "terms": {
        "field": "IP",	// 目标字段
        "size": 3000,   // 相当于SQL中的limit
        "order": {			// 设置桶的排序
          "_count": "desc"
        },
        "min_doc_count": 5	// 过滤匹配文档数量小于给定值的桶
      },
      "aggs": {
        "unique_uid":{
           "cardinality":{"field":"uid"}
         },
        "max_server_time":{
           "max":{"field":"serverTime"}
         }
      }
    }
  },
  "size": 0
}
```



使用histogram查询

```json

{
  "query": {
    "bool": {
      "must": [
        {
          "term": {
            "appKey": "this is a appKey"
          }
        },
        {
          "range": {
            "serverTime": {
              "gte": 1610236800,
              "lt": 1612915200
            }
          }
        }
      ]
    }
  },

  "aggs": {
     "count_by_day": {
      "histogram": {        // 用于统计pv
        "field":    "serverTime",
        "interval": 86400,
        "offset":   57600,  // offset是为了能整除86400
        "order" : { "_key" : "asc" }
      },
      "aggs":{
        "unique_uid":{
          "cardinality":{  // 用于统计uv
            "field":"uid"
          }
        }
      }
    }
 },
  
 "size": 0
}
```





----

#### 8 FAQ



##### 01 上传json数据到es

```shell
curl -H 'Content-Type: application/x-ndjson' -s -XPOST http://172.20.0.214:50900/_bulk --data-binary @user_behaviour.json
```



##### 02 删除上传的json数据

```shell
curl -X DELETE "localhost:9200/user_behaviour"
```



##### 03 用from和size控制分页

```json
GET user_behaviour/_search?pretty=false
{
  "from": 0, 
  "size": 2,  
  "query": {
    "term": {
      "retain_days": {
              "value": "0"
            }
    }
  }
}
```



##### 04 搜索结果不稳定问题

原因：数据分布在不同分片，不同分片的分数计算最终结果是不一样，所以导致两次一样的查询最终搜索出来的结果不一样。

方案：使用[preference=session_id](https://www.elastic.co/guide/en/elasticsearch/reference/6.8/search-request-preference.html)

> 返回值中的took是指查询消耗的毫秒数



```json
GET /_search?preference=xyzabc123
{
    "query": {
        "match": {
            "title": "elasticsearch"
        }
    }
}
```



##### 05 搜索模式

1. index是database
2. type是table
3. field是column



|                        |                                          |      |
| ---------------------- | ---------------------------------------- | ---- |
| _search                | 所有索引，所有type下的所有数据都搜索出来 |      |
| /index1/_search        | 指定一个index，搜索其下所有type的数据    |      |
| /index1,index2/_search | 同时搜索2个index下的数据                 |      |
| /\*1,\*2/_search         | 按照通配符去匹配多个索引 |      |
| /index1/type1/_search | 搜索一个index下指定type的数据 | |
| /index1/type1,type2/_search | 搜索一个index下多个type的数据 | |
| /index1,index2/type1,type2/_search | 搜索多个index下多个type的数据 | |
| /all/type1,type2/_search:\_all | 搜索所有index下指定type的数据 | |



##### 06 排序

```json
{
    "query":{
        "bool":{
            "filter":[
                ...
            ]
        }
    },
   "sort": [
    {
      "serverTime": {
        "order": "desc"
      }
    }
  ],
  "size":100
}
```






----

#### 9 References

1. [Query DSL](https://www.elastic.co/guide/en/elasticsearch/reference/7.9/query-dsl.html)
2. [全文搜索 (三) - match查询和bool查询的关系，提升查询子句](https://blog.csdn.net/dm_vincent/article/details/41743955)
3. [ES中search中参数讲解](https://blog.csdn.net/paicMis/article/details/83833783)
4. [Es Bucket聚合(桶聚合) Terms Aggregation与Significant Terms Aggregation](https://cloud.tencent.com/developer/article/1443591)
5. [Elasticsearch: The Beginner’s Cookbook](https://medium.com/@animeshblog/elasticsearch-the-beginners-cookbook-1cf30f98218)
6. 

