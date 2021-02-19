



1. 通过 `curl -XGET "http://localhost:8200/name_of_index/_mapping?pretty"` 可以查询到mapping信息
2. 创建 mapping 时，可以为keyword类型的string指定 `ignore_above` ，用来限定字符长度。超过 `ignore_above` 的字符会被存储，但不会被索引。注意，是字符长度，一个英文字母是一个字符，一个汉字也是一个字符。在动态生成的 mapping 中，`keyword`类型会被设置`ignore_above: 256`
3. norms：字段长度、字频等归一化属性，用来计算文档/字段得分(Score)的"调节因子"





| 类型    | 描述                                                         |
| ------- | ------------------------------------------------------------ |
| keyword | 1. 作为一个整体，不会被分解为一个个term<br />2. 默认关闭norms |
| text    | 1. 会被Analyzer分析，并拆解为一个个term<br />2. 默认开启norms |
|         |                                                              |
|         |                                                              |
|         |                                                              |
|         |                                                              |
|         |                                                              |
|         |                                                              |







----

#### 09 References

1. [Elasticsearch 7 : 使用 ignore_above 限制字符串长度](https://www.letianbiji.com/elasticsearch/es7-ignore-above.html)
2. 