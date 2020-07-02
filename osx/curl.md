



----

curl

| 按键                                  | 详解                                        |
| ------------------------------------- | ------------------------------------------- |
| curl -k https://ssl.url -o output.txt | 跳过ssl认证，直接下载                       |
| curl -G http://url > output.txt       | 使用get会等待下载完成再结束命令，post则不然 |
| curl -H "site:MAIN" http://url        | 传入header, -H 可重复指定                   |
| curl -v http://url                    | 显示调用详情，比如301重定向信息             |

