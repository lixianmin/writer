



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





|          |                                                  |      |
| -------- | ------------------------------------------------ | ---- |
| aggs     | 聚合                                             |      |
| bool     | 支持must, filter                                 |      |
| filtered |                                                  |      |
| match    | match的多词查询就是bool + or的term查询           |      |
| query    | 支持bool, term, match, multi_match, match_phrase |      |
| term     | 完全匹配，不分词                                 |      |
|          |                                                  |      |



---

#### 0x02 bool联合查询

bool联合查询指多个子条件必须同时成立，因此是&&操作

| 语句     | 描述                                             |      |
| -------- | ------------------------------------------------ | ---- |
| must     | 文档必须完全匹配条件                             |      |
| should   | should下面会带一个以下的条件，只要一个满足就可以 |      |
| must_not | 文档必须不匹配条件                               |      |



1. must下面可能是match, term, regex, multi_match查询

2. match查询可看作是bool查询的简化写法

```json
// match查询的多词查询只是简单地将生成的term查询包含在了一个bool查询中。以下两个查询是等价的：
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

// 使用and操作符时，所有的term查询都以must语句被添加，因此所有的查询都需要匹配。以下两个查询是等价的：
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

1. [Request Body Search](https://www.elastic.co/guide/en/elasticsearch/reference/6.7/search-request-body.html#search-request-body)
2. [全文搜索 (三) - match查询和bool查询的关系，提升查询子句](https://blog.csdn.net/dm_vincent/article/details/41743955)
3. 

