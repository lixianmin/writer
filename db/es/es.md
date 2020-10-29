



----

#### 0x01 Basics

```shell
# 启动服务
brew services list
brew services start elasticsearch
brew services start kibana
```



1. kibana的x-pack本身有shield（身份认证）与reporting（csv下载）的功能，但是在免费版本中没有
2. 



|         |                          |      |
| ------- | ------------------------ | ---- |
| _bulk   | 上传数据                 |      |
| _search | 搜索数据                 |      |
| _source | 查询结果中实际的数据部分 |      |



-----

#### 0x02 叶子查询

1. 叶子查询直接带\<field\>子节点
2. \<field\>节点的子节点会有一些参数用于约束叶子查询的行为
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

1. 精确匹配字段，一般用于price, price id, username等可以精确匹配的场景
2. 避免对text类型的字段使用term查询。因为es对text类型的字段会做预处理（比如删除标点符号、分词、转换成小写等），所以很难使用term子句精确匹配，建议使用match子句。



##### 02 [terms](https://www.elastic.co/guide/en/elasticsearch/reference/7.9/query-dsl-terms-query.html)

1. term查询的复数形式
2. 多个参考值之间是or的关系



##### 03 [IDs](https://www.elastic.co/guide/en/elasticsearch/reference/7.9/query-dsl-ids-query.html)

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



##### 04 [range](https://www.elastic.co/guide/en/elasticsearch/reference/7.9/query-dsl-range-query.html)

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



1.  query参数描述搜索词列表，默认行为与baidu类似，以or关系按评分返回匹配的doc
2.  如果需要改为and的关系，则需要加一个`"operator":"and"`的参数



-----

#### 0x03 [bool复合查询](https://www.elastic.co/guide/en/elasticsearch/reference/7.9/query-dsl-bool-query.html)

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



1. bool复合查询指多个子条件必须同时成立，因此是&&操作
2. 支持must, filter, should, must_not这4个子句
3.  minimum_should_match用在should子句后面



##### 01 must

1. doc必须匹配，跟filter的不同是会影响score

```json
// 以下两个查询是等价的：
{
    "match": {
        "title": {
            "query":    "brown fox",
            "operator": "and"
        }
    }
}

{
  "bool": {
    "must": [
      { "term": { "title": "brown" }},
      { "term": { "title": "fox"   }}
    ]
  }
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

#### 0x08 FAQ



##### 上传json数据到es

```shell
curl -H 'Content-Type: application/x-ndjson' -s -XPOST http://172.20.0.214:50900/_bulk --data-binary @user_behaviour.json
```



##### 删除上传的json数据

```shell
curl -X DELETE "localhost:9200/user_behaviour"
```



##### 使用from和size控制分页

```json
GET user_behaviour/_search?pretty=false
{
  "from": 0, "size": 2,  
  "query": {
    "term": {
      "retain_days": {
              "value": "0"
            }
    }
  }
}
```





----

#### 0x09 References

1. [Query DSL](https://www.elastic.co/guide/en/elasticsearch/reference/7.9/query-dsl.html)
2. [全文搜索 (三) - match查询和bool查询的关系，提升查询子句](https://blog.csdn.net/dm_vincent/article/details/41743955)
3. 

