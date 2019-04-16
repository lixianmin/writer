



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



|         |          |      |
| ------- | -------- | ---- |
| _bulk   | 上传数据 |      |
| _search | 搜索数据 |      |
|         |          |      |





|          |                  |      |
| -------- | ---------------- | ---- |
| aggs     | 聚合             |      |
| filtered |                  |      |
| query    | 查询             |      |
| term     | 完全匹配，不分词 |      |
|          |                  |      |



---

#### 0x02 bool联合查询

bool联合查询指多个子条件必须同时成立，因此是&&操作

|          |                                                  |      |
| -------- | ------------------------------------------------ | ---- |
| must     | 文档必须完全匹配条件                             |      |
| should   | should下面会带一个以下的条件，只要一个满足就可以 |      |
| must_not | 文档必须不匹配条件                               |      |



1. must下面可能是match, term, regex, multi_match查询
2. 





----

#### 0x08 Q&A



##### 上传json数据到es

```shell
curl -H 'Content-Type: application/x-ndjson' -s -XPOST http://172.20.0.214:50900/_bulk --data-binary @user_behaviour.json
```



##### 删除上传的json数据

```shell
curl -X DELETE "localhost:9200/user_behaviour"
```



----

#### 0x09 References

1. 

