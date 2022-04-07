



----

curl

| 命令                                                         | 详解                                        |
| ------------------------------------------------------------ | ------------------------------------------- |
| curl -k https://ssl.url -o output.txt                        | 跳过ssl认证，直接下载                       |
| curl -G http://url > output.txt                              | 使用get会等待下载完成再结束命令，post则不然 |
| curl "http://url?key=123&token=abc"                          | 在url中传递多个参数                         |
| curl -H "site:MAIN" http://url                               | 传入header, -H 可重复指定                   |
| curl -d "user=user1&pass=abcd" https://example.com/login<br />curl -d '{ "user":"user1, "pass":"abcd"}' -X POST https://example.com/login | 发送post请求，-d代表data，-X POST可省略     |
| curl -s                                                      | 不显示Total, Received等统计信息             |
| curl -T xxx.txt                                              | 本地文件                                    |
| curl -v http://url                                           | 显示调用详情，比如301重定向信息             |



